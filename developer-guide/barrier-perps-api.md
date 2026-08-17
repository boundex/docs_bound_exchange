# Barrier Perps API

Programmatic access to Barrier Perps — pick two price barriers around the current market price, bet on which one is reached first. See [Barrier Perps](../barrier-perps/README.md) for the product overview.

Positions are **non-custodial**: Bound builds and prices every action, but the user's own wallet signs it and the position lives in the user's own Hyperliquid account. Bound never holds the key.

{% hint style="info" %}
Endpoint paths use the internal name `which-first` — the product's former name. Paths are stable; only the product name changed.
{% endhint %}

**Base URL:** `https://api.bound.exchange`

All endpoints require a JWT (see the [Authentication Guide](../developer-guide-backup/authentication-guide.md)) and are rate-limited.

```http
Authorization: Bearer <access_token>
```

---

## Response wrapper

Every response is wrapped by the API's response interceptor. The payload lives in `data` — **never at the root**.

```ts
type ApiResponse<T> = {
  code: string;      // "1" on success; a 26xxx string on a Barrier Perps error
  message: string;   // "common.success" or an error message key
  details?: string;  // human-readable detail on some errors
  data: T;           // the payload
  metaData?: {       // present on paginated endpoints only
    totalItems: number;
    currentPage: number;
    pageSize: number;
    totalPages: number;
  };
};
```

### TypeScript helper

Every example below uses this helper. It keeps `code` on the thrown error — the recovery
patterns further down all branch on it, so a helper that discards it will not work.

```ts
const HOST = 'https://api.bound.exchange';

class ApiError extends Error {
  constructor(public code: string, message: string, public details?: string) {
    super(`${code}: ${message}${details ? ' — ' + details : ''}`);
  }
}

async function api<T>(path: string, init?: RequestInit & { jwt?: string }): Promise<T> {
  const headers: Record<string, string> = { 'Content-Type': 'application/json' };
  if (init?.jwt) headers.Authorization = `Bearer ${init.jwt}`;
  const res = await fetch(`${HOST}${path}`, { ...init, headers: { ...headers, ...init?.headers } });
  const body = await res.json();
  if (body.code !== '1') throw new ApiError(body.code, body.message, body.details);
  return body.data as T;
}
```

### `HlAction`

Several endpoints return an unsigned Hyperliquid action. Throughout this page:

```ts
type HlAction = Record<string, unknown>;
```

It is **opaque** — Bound builds it, you sign it byte for byte and never edit it. Bound
re-derives the expected body when you relay it back and rejects any modification with `26027`.

---

## Where each request goes

Three different destinations — getting this wrong is the most common integration mistake.

| What | Destination |
| --- | --- |
| Build / price / read a position | **Bound API** (this document) |
| Signed **bet bundles** — entry, cash-out | **Bound API** → `POST /:id/execute` (Bound verifies, relays, records order ids) |
| Signed **single actions** — builder fee, referral, withdraw | **Hyperliquid `/exchange`** directly. No Bound endpoint. |
| Signed **deposit transactions** | **HyperEVM RPC** directly (`eth_sendRawTransaction`). No Bound endpoint. |

---

## Before you start

Three things decide whether the signing steps below will work at all. Check them before
writing any bet flow — two of them are Hyperliquid constraints, not Bound ones.

### The signing wallet

Every action Bound returns is signed by **the user's own EVM wallet** — the same wallet
passed as `userAddress`. Bound never holds the key and never signs on the user's behalf.

Use [`@nktkas/hyperliquid`](https://www.npmjs.com/package/@nktkas/hyperliquid) for signing.
It handles msgpack hashing, EIP-712 domains, and address lower-casing — a checksummed
address inside an L1 action makes the signature recover to the wrong signer.

{% hint style="warning" %}
**External wallets cannot sign L1 actions.** Hyperliquid L1 actions (`update_leverage`,
`entry`, `cancel`, `cash_out_close`, `set_referrer`) are signed over **chain id 1337**, which
browser wallets refuse.

If your signer is an external wallet, provision a **Hyperliquid agent**: the master wallet
approves the agent once, then the agent key signs the L1 actions locally. EIP-712 actions
(`approve_builder_fee`, `withdraw`) are unaffected — the master signs those directly.

A wallet whose key you hold in-process can sign everything itself and needs no agent.
{% endhint %}

### Account type

Bound rejects **Portfolio Margin** accounts with `26017`. Unified and standard/manual
accounts both work — Bound reads the available collateral differently for each (under a
unified account the perps collateral sits in the **spot** balance).

{% hint style="info" %}
This is the API's only account-type rule. Bound's own frontend additionally converts
accounts to **unified** before betting, but that is a product decision on its side, not
something this API requires of you.
{% endhint %}

### Nonces

Every signed action carries a `nonce` in milliseconds. Hyperliquid requires it to be
**unique and strictly increasing per signer**, and checks it against a time window.

One signer covers **all** assets and flows. Two flows that sign in the same millisecond —
placing a bet while a cash-out is being signed, say — would both take `Date.now()` and
collide, and Hyperliquid rejects one of the bundles.

**`Date.now()` alone is not enough.** Allocate nonces from a single monotonic source that
stays anchored to wall-clock time but always returns a value greater than the last one it
handed out.

---

## Lifecycle

Endpoints below are documented in this order.

```
Setup (once per wallet)
  1. POST /deposit                 → sign 2 HyperEVM txs → broadcast yourself
  2. POST /referral                → signL1Action        → POST to HL /exchange
  3. POST /builder-fee-approval    → signUserSignedAction→ POST to HL /exchange

Place a bet
  4. GET  /limits                  → validate bounds client-side first
  5. POST /quote                   → preview leverage / payout
  6. POST /proposals               → betId + unsigned actions   (state: proposed)
  7. POST /:id/execute             → relay signed entry bundle
     └─ Bound's monitor sees the fill (~30s)                    (state: active)

Exit
  8. POST /:id/cash-out            → unsigned cancel + close → back to /:id/execute
     └─ or let the venue resolve it: TP fill → won · liquidation → lost

Read / off-ramp
  9. GET  /which-first             → list your bets
 10. GET  /which-first/:id         → one bet
 11. POST /withdraw                → signUserSignedAction → POST to HL /exchange
```

**States:** `proposed` · `active` · `won` · `lost` · `cashed_out` · `voided` · `aborted`

{% hint style="info" %}
**How to read each endpoint below.** The interactive block shows authentication,
parameters and the request body straight from the live OpenAPI spec — that is the
authoritative request contract.

The spec does **not** yet carry response schemas, so the block's response section renders
empty. Until it does, the **Response data** section written under each block is the
authoritative description of what comes back.
{% endhint %}

---

## 1. POST `/api/which-first/deposit` — fund the account

Returns an **unsigned two-transaction HyperEVM plan** that moves USDC into the wallet's Hyperliquid account. USDC is the perps collateral and uses a pull-based deposit contract.

{% openapi src="https://api.bound.exchange/api/docs/openapi-bound.yaml" path="/api/which-first/deposit" method="post" %}
https://api.bound.exchange/api/docs/openapi-bound.yaml
{% endopenapi %}


**Response data:**

```ts
type DepositPlan = {
  usdcToken: string;       // USDC ERC-20 address
  depositContract: string; // pull-based deposit contract
  rpcUrl: string;          // broadcast both signed txs here yourself
  from: string;            // the signing wallet
  amount: string;
  txs: [EvmTx, EvmTx];     // [approve, deposit] — sign and broadcast IN ORDER
};

type EvmTx = { chainId: number; to: string; data: string; value: string };
```

**Example:**

```ts
const plan = await api<DepositPlan>('/api/which-first/deposit', {
  method: 'POST', jwt,
  body: JSON.stringify({ userAddress: '0xc12b0a4a5fdd42d1f4aa2a08c2193d1ac49c8167', amount: '10' }),
});
```

```jsonc
{
  "usdcToken": "0xb88339…",
  "depositContract": "0x6b9e77…",
  "rpcUrl": "https://…hyperevm-rpc",
  "from": "0x…",
  "amount": "10",
  "txs": [
    { "chainId": 999, "to": "0xb88339…", "data": "0x095ea7b3…", "value": "0" }, // approve
    { "chainId": 999, "to": "0x6b9e77…", "data": "0x2b2dfd2c…", "value": "0" }  // deposit
  ]
}
// mainnet: chainId 999 · testnet: chainId 998
```

{% hint style="danger" %}
**This signs completely differently from every other endpoint.** Each `txs[i]` is a normal **HyperEVM transaction** — sign and broadcast them yourself with `eth_sendRawTransaction` on the plan's `rpcUrl`. Do **not** use `signL1Action` or the Hyperliquid SDK.

1. **Sign both with the same wallet you bet with** — Hyperliquid credits the *sender's* address.
2. **Broadcast approve first, deposit second, with consecutive nonces.** A gapped pair broadcasts fine but the deposit sits pending forever.
3. Use the plan's `chainId`.

The `approve` is for the **exact** amount, so send **both** transactions on every deposit — a previous allowance was consumed by its deposit.

Prerequisites: the wallet holds USDC on HyperEVM plus a little HYPE for gas.
{% endhint %}

**Confirming it landed.** Transaction hashes only prove broadcast, not credit. Poll Hyperliquid's `userNonFundingLedgerUpdates` for a `spotTransfer` entry with `token: "USDC"` and `destination` = the wallet. Do **not** gate on a raw balance delta — an open position's funding and margin activity move the balance at the same time.

**Errors:** `26018` invalid amount · `26016` no eligible EVM wallet

---

## 2. POST `/api/which-first/referral` — referral opt-in (one-time)

Returns an unsigned `setReferrer` action, or `null`. Best-effort: it never blocks a bet.

{% openapi src="https://api.bound.exchange/api/docs/openapi-bound.yaml" path="/api/which-first/referral" method="post" %}
https://api.bound.exchange/api/docs/openapi-bound.yaml
{% endopenapi %}


{% hint style="warning" %}
**Time-sensitive.** Hyperliquid only lets a wallet set a referrer while its cumulative volume is under **$10k**. A wallet that trades past that while unreferred can never be captured — so call this at the wallet's first trade.

The urgency is relative to that volume ceiling, **not** to the builder-fee approval. The two actions are independent and may be called in either order.
{% endhint %}

**Response data:**

```ts
type ReferralResponse = { kind: 'set_referrer'; action: HlAction } | null;
```

`data` is **`null`** when no referral code is configured, or the wallet already has a referrer (Hyperliquid is set-once). Handle the null — never prompt a doomed signature.

**Example:**

```ts
const referral = await api<ReferralResponse>('/api/which-first/referral', {
  method: 'POST', jwt,
  body: JSON.stringify({ userAddress: '0xc12b…' }),
});

if (referral) {
  const signature = await signL1Action({ wallet, action: referral.action, nonce, isTestnet });
  await fetch('https://api.hyperliquid.xyz/exchange', {          // NOT the Bound API
    method: 'POST',
    body: JSON.stringify({ action: referral.action, nonce, signature }),
  });
}
```

```jsonc
{ "kind": "set_referrer", "action": { "type": "setReferrer", "code": "BOUND" } }
```

Sign with `signL1Action` — this is an **L1 action**, not EIP-712.

**Errors:** `26020` referral not set (only when the `referralRequired` setting is on) · `26016` no eligible EVM wallet

---

## 3. POST `/api/which-first/builder-fee-approval` — approve builder fee (one-time)

Returns an unsigned `approveBuilderFee` action. Sign once per wallet; afterwards the `builderFeeNotApproved` gate stops firing.

{% openapi src="https://api.bound.exchange/api/docs/openapi-bound.yaml" path="/api/which-first/builder-fee-approval" method="post" %}
https://api.bound.exchange/api/docs/openapi-bound.yaml
{% endopenapi %}


**Response data:**

```ts
type BuilderFeeApproval = { kind: 'approve_builder_fee'; action: HlAction };
```

**Example:**

```ts
const approval = await api<BuilderFeeApproval>('/api/which-first/builder-fee-approval', {
  method: 'POST', jwt, body: JSON.stringify({}),
});
```

```jsonc
{
  "kind": "approve_builder_fee",
  "action": {
    "type": "approveBuilderFee",
    "hyperliquidChain": "Mainnet",
    "signatureChainId": "0xa4b1",
    "maxFeeRate": "0.05%",
    "builder": "0x…"
  }
}
```

Sign with `signUserSignedAction` (EIP-712), then POST the signed envelope directly to Hyperliquid `/exchange`.

**Errors:** `26009` builder fee not approved (raised later, by `/proposals`, if this was skipped)

---

## 4. GET `/api/which-first/limits` — bound constraints

Per-asset constraints so you can validate bounds **without** a `/quote` round-trip. Same source as the server-side gates, so values never drift.

{% openapi src="https://api.bound.exchange/api/docs/openapi-bound.yaml" path="/api/which-first/limits" method="get" %}
https://api.bound.exchange/api/docs/openapi-bound.yaml
{% endopenapi %}


`coin` is optional and defaults to the launch asset. URL-encode HIP-3 names —
`?coin=xyz%3ATSLA`, not `?coin=xyz:TSLA`.

**Response data:**

```ts
type Limits = {
  coin: string;
  mark: string;                 // snapshot at fetch time
  maxLeverage: number;
  maxBoundDistance: string;     // each bound ≤ this fraction from mark
  minAgainstDistance: {         // liquidation-side floor, per side
    upper_first: string;
    lower_first: string;
  };
};
```

**Example:**

```ts
const limits = await api<Limits>('/api/which-first/limits?coin=BTC', { jwt });
```

```jsonc
{
  "coin": "BTC",
  "mark": "61000",
  "maxLeverage": 40,
  "maxBoundDistance": "0.1",        // each bound ≤ 10% from mark
  "minAgainstDistance": {
    "upper_first": "0.015158",      // LONG:  lower bound ≥ 1.5158% BELOW mark
    "lower_first": "0.014846"       // SHORT: upper bound ≥ 1.4846% ABOVE mark
  }
}
```

The `minAgainstDistance` fractions are **stable per asset** — cache them and combine with your own live mark:

```ts
const d = limits.minAgainstDistance[side];
// The "against" bound is the OPPOSITE of your bet side (= the liquidation side):
//   upper_first (LONG):  against = lower → require lower ≤ mark * (1 - d)
//   lower_first (SHORT): against = upper → require upper ≥ mark * (1 + d)
// Both bounds must also stay within mark * (1 ± maxBoundDistance).
```

Validating here avoids `26005` and `26004` on `/quote`.

{% hint style="info" %}
`/limits` accepts no wager and enforces neither the configured wager range nor Hyperliquid's minimum order notional. Only `/proposals` does both.
{% endhint %}

**Errors:** `26022` unsupported asset · `26014` venue unreachable (transient — retry)

---

## 5. POST `/api/which-first/quote` — price a bet

Pure pricing preview. No wallet gates, no persistence, no side effects.

{% openapi src="https://api.bound.exchange/api/docs/openapi-bound.yaml" path="/api/which-first/quote" method="post" %}
https://api.bound.exchange/api/docs/openapi-bound.yaml
{% endopenapi %}


**Response data:**

```ts
type Construction = {
  direction: 'long' | 'short';
  leverage: number;          // integer, rounded down
  notional: string;          // leverage × wager
  coinSize: string;
  chosenBound: string;       // the bound you bet → take-profit
  losingBound: string;       // the opposing bound → liquidation
  takeProfitPrice: string;
  liquidationPrice: string;  // pre-trade estimate at the mark
  payoutMultiple: string;    // profit ÷ wager
  payoutEstimate: string;    // profit in USDC if won, before fees and funding
  maxSlippage: string;
  entryPrice: string;        // the mark used to price
  wager: string;
};
```

**Example:**

```ts
const quote = await api<Construction>('/api/which-first/quote', {
  method: 'POST', jwt,
  body: JSON.stringify({
    side: 'upper_first', upperBound: '63500', lowerBound: '59000', wager: '20', coin: 'BTC',
  }),
});
```

```jsonc
{
  "direction": "long",
  "leverage": 17,
  "notional": "340",
  "coinSize": "0.0056",
  "chosenBound": "63500",
  "losingBound": "59000",
  "takeProfitPrice": "63500",
  "liquidationPrice": "58656.39",
  "payoutMultiple": "0.66",        // win pays +66% of the wager
  "payoutEstimate": "13.25",
  "maxSlippage": "0.01",
  "entryPrice": "61000",
  "wager": "20"
}
```

{% hint style="warning" %}
`wager` is **isolated margin**, not order notional.

`/quote` deliberately does **not** enforce the configured wager range or Hyperliquid's $10 minimum order notional — a structurally valid wager can produce a construction that cannot be proposed. Treat `/proposals` as the authoritative gate before asking the user to sign.

`payoutMultiple` is **profit ÷ wager**. To show a total-return multiple, use `1 + payoutMultiple`.
{% endhint %}

**Errors:** `26003` invalid bounds · `26004` bound cap exceeded · `26005` against-distance too small · `26010` price not fresh · `26013` construction failed · `26022` unsupported asset

---

## 6. POST `/api/which-first/proposals` — create the bet

Runs every gate, persists a `proposed` bet (which holds the per-asset slot), and returns the actions to sign.

{% openapi src="https://api.bound.exchange/api/docs/openapi-bound.yaml" path="/api/which-first/proposals" method="post" %}
https://api.bound.exchange/api/docs/openapi-bound.yaml
{% endopenapi %}


The body is the `/quote` fields plus `userAddress` — the EVM wallet that signs and owns the
Hyperliquid position.

**Response data:**

```ts
type ProposalResult = {
  betId: string;
  state: 'proposed';
  proposalExpiresAt: string;      // ISO — sign and execute BEFORE this
  construction: Construction;     // same shape as /quote
  unsignedActions: UnsignedAction[];
};

type UnsignedAction = { kind: 'update_leverage' | 'entry'; action: HlAction };
```

**Example:**

```ts
const proposal = await api<ProposalResult>('/api/which-first/proposals', {
  method: 'POST', jwt,
  body: JSON.stringify({
    side: 'upper_first', upperBound: '63500', lowerBound: '59000',
    wager: '20', coin: 'BTC', userAddress: '0xc12b…',
  }),
});
```

```jsonc
{
  "betId": "6a3d…",
  "state": "proposed",
  "proposalExpiresAt": "2026-06-26T10:30:00.000Z",
  "construction": { "leverage": 17, "takeProfitPrice": "63500", "…": "…" },
  "unsignedActions": [
    { "kind": "update_leverage", "action": { "type": "updateLeverage", "…": "…" } },
    { "kind": "entry",           "action": { "type": "order", "grouping": "normalTpsl", "…": "…" } }
  ]
}
```

{% hint style="warning" %}
Unlike `/quote`, this enforces the configured wager range (default 10–100,000 USDC) **and** requires `price × coinSize ≥ $10` for **both** order wires — entry and attached take-profit. Do not pre-validate that as `10 / leverage`: rounding and the take-profit price can push the real floor higher.

Only **one open bet per asset per account** — including from a different wallet on the same account. A retry while one is open returns `26007`.
{% endhint %}

**Errors:** `26006` wager out of range · `26007` existing exposure · `26008` insufficient balance · `26009` builder fee not approved · `26011` proposal expired · `26017` portfolio margin unsupported · `26030` insufficient liquidity · `26032` order notional too small

---

## 7. POST `/api/which-first/{id}/execute` — relay the signed bundle

The only signed bodies Bound relays. Sign the `unsignedActions` from `/proposals` (entry) or `/cash-out`, keep each original `kind`, and send them **in order**.

{% openapi src="https://api.bound.exchange/api/docs/openapi-bound.yaml" path="/api/which-first/{id}/execute" method="post" %}
https://api.bound.exchange/api/docs/openapi-bound.yaml
{% endopenapi %}


**Response data:** the updated bet.

**Example:**

```ts
await api('/api/which-first/6a3d…/execute', {
  method: 'POST', jwt,
  body: JSON.stringify({
    actions: [
      { kind: 'update_leverage', action: a0, nonce: n0, signature: s0 },
      { kind: 'entry',           action: a1, nonce: n1, signature: s1 },
    ],
  }),
});
```

Bound relays each action to Hyperliquid, extracts the order ids itself, and persists `venueRefs`. **The client never reports order ids.**

### Guards

**Relay-once.** `26028` fires in three cases — handle each:

1. The order ids are **already recorded** (the common retry) → **re-read the bet**, the result is there. Never re-sign; a re-signed resend opens a second live position.
2. Another relay for the same bet is **in flight** (a ~60s claim serializes concurrent executes) → show "processing", retry shortly.
3. A retry after an interrupted relay found venue evidence — outcome unknown, an operator was alerted → surface support contact.

**Structural verification.** Each signed action must equal the bundle Bound built, field for field: action type per kind, exact legs, side/size/reduce-only, the recomputed entry and trigger prices, time-in-force flags, grouping, the cancel target, leverage, and the builder fee at or above the quoted rate. A modified body is rejected with `26027` before touching the venue.

**Per-order rejection.** Hyperliquid reports order rejections *inside* a success envelope. Bound detects them and returns `26025` with Hyperliquid's own message — nothing is persisted and the bet does not look executed. **`26025` is the only definitive "nothing landed" verdict**, and it **releases the relay claim** — so an immediate retry after a rejection (for example, re-cashing out at a fresher price) is allowed. Only ambiguous transport failures hold the claim for its full ~60s.

**Accepted without an order id.** Hyperliquid sometimes returns a bare `waitingForTrigger` / `waitingForFill` — the order *is* live but carries no id.

- The **take-profit leg** commonly does this. The bet records `venueRefs.takeProfitOid = null` for up to one monitor cycle (~30s) until the monitor self-heals it. A cash-out in that window is refused with `26012` — retry after a few seconds.
- If a **money leg** (entry or close) returns no id, Bound returns `26014`, **keeps** the relay claim, and alerts an operator. Do **not** re-sign — the order may be live. Re-read the bet and contact support.

**Errors:** `26011` proposal expired · `26012` not active · `26019` not proposed · `26023` invalid bundle · `26025` order rejected · `26027` action mismatch · `26028` already executed · `26029` HIP-3 execution disabled

---

## 8. POST `/api/which-first/{id}/cash-out` — exit early

Returns an unsigned bundle that cancels the resting take-profit and closes the position reduce-only. Only for an `active` bet.

{% openapi src="https://api.bound.exchange/api/docs/openapi-bound.yaml" path="/api/which-first/{id}/cash-out" method="post" %}
https://api.bound.exchange/api/docs/openapi-bound.yaml
{% endopenapi %}


**Response data:**

```ts
type CashOutResult = {
  betId: string;
  unsignedActions: [
    { kind: 'cancel';         action: HlAction }, // cancel the resting take-profit
    { kind: 'cash_out_close'; action: HlAction }, // reduce-only close
  ];
};
```

**Example:**

```ts
const bundle = await api<CashOutResult>('/api/which-first/6a3d…/cash-out', {
  method: 'POST', jwt, body: JSON.stringify({}),
});
// sign both, then send them to POST /:id/execute — same as the entry bundle
```

Repeating a cash-out is a no-op; a bet that is no longer active returns `26012`.

**Errors:** `26001` bet not found · `26002` ownership mismatch · `26012` not active

---

## 9. GET `/api/which-first` — list your bets

Paginated. Regular users always see only their own bets — the account scope is enforced server-side.

{% openapi src="https://api.bound.exchange/api/docs/openapi-bound.yaml" path="/api/which-first" method="get" %}
https://api.bound.exchange/api/docs/openapi-bound.yaml
{% endopenapi %}


**Query:** filters use the `field_eq=value` grammar. A bare `?state=…` does **not** filter.

| Param | Type | Description |
| --- | --- | --- |
| `state_eq` | `string` | One of `proposed` · `active` · `won` · `lost` · `cashed_out` · `voided` · `aborted` |
| `evmAddress_eq` | `string` | One wallet. **Must be lower-cased** — addresses are stored lower-cased and are not normalized for you. |
| `coin_eq` | `string` | Canonical asset name |
| `side_eq` | `string` | `upper_first` or `lower_first` |
| `createdAt_gte` / `createdAt_lte` | `number` | Epoch ms range |
| `activatedAt_gte` / `activatedAt_lte` | `number` | Epoch ms range |
| `resolvedAt_gte` / `resolvedAt_lte` | `number` | Epoch ms range |
| `page` | `number` | Default `1` |
| `pageSize` | `number` | Default `50` |
| `sort` | `string` | e.g. `-createdAt` for newest first |

**Response data:** an array of bets. `metaData` on the envelope carries the pagination totals.

**Example:**

```ts
const res = await fetch(
  `${HOST}/api/which-first?state_eq=active&evmAddress_eq=0xc12b…&page=1&pageSize=50`,
  { headers: { Authorization: `Bearer ${jwt}` } },
).then((r) => r.json());

const bets  = res.data;                 // Bet[]
const total = res.metaData.totalItems;  // use this for page count, never bets.length
```

---

## 10. GET `/api/which-first/{id}` — read one bet

Owner only.

{% openapi src="https://api.bound.exchange/api/docs/openapi-bound.yaml" path="/api/which-first/{id}" method="get" %}
https://api.bound.exchange/api/docs/openapi-bound.yaml
{% endopenapi %}


**Response data:** a bet, whose fields are grouped into three sub-objects.

```ts
type Bet = {
  _id: string;                  // the list returns `_id`; only /proposals returns `betId`
  coin: string;
  evmAddress: string;           // stored lower-cased
  side: 'upper_first' | 'lower_first';
  upperBound: string;           // the bounds as you submitted them
  lowerBound: string;
  wager: string;                // isolated margin, USDC

  construction: BetConstruction;  // the priced snapshot — targets, leverage, payout

  // Lifecycle
  state: 'proposed' | 'active' | 'won' | 'lost' | 'cashed_out' | 'voided' | 'aborted';
  proposalExpiresAt: string;      // ISO
  activatedAt: string | null;     // ISO — entry fill verified → active
  resolvedAt: string | null;      // ISO — reached won / lost / cashed_out / voided
  abortReason: 'proposal_expired' | 'user_abandoned' | null;
  voidReason: 'tp_cancelled' | 'margin_changed' | 'position_closed' | 'position_resized' | 'other' | null;
  cashOutPending: boolean;        // true once a cash-out was requested
  boundBreachFlagged: boolean;    // the real fill put liquidation inside the losing bound

  venueRefs: {                    // Hyperliquid order ids
    entryOid: string | null;
    takeProfitOid: string | null; // null for ≤1 monitor cycle right after execute
    closeOid: string | null;
  };

  actuals: {                      // post-fill — every field null until the venue event lands
    actualEntryPrice: string | null;
    actualLiquidationPrice: string | null;  // the on-venue value — use this once active
    actualPayoutMultiple: string | null;
    actualCoinSize: string | null;
    actualMarginUsed: string | null;
    fundingAtActivation: string | null;
    finalProceeds: string | null;  // GROSS realized PnL — before fees and funding
    feesPaid: string | null;       // close-side exchange fee
    fundingPaid: string | null;    // signed; negative = paid
    netProceeds: string | null;    // authoritative NET — nullable, see below
  };

  createdAt: number;              // epoch ms — NOT an ISO string
  updatedAt: number;              // epoch ms — bumped by every write

  // Internal bookkeeping — currently serialized, but do not build on these.
  accountId: string;              // Bound's internal account id
  relayClaimedAt: string | null;  // ISO — internal relay-once claim stamp
  deletedAt: string | null;       // soft-delete marker, always null on a live bet
};
```

`BetConstruction` is **not** the same shape `POST /quote` returns:

- it **adds** four venue-snapshot fields recorded at proposal time — `assetIndex`,
  `szDecimals`, `maintenanceMarginFraction`, `builderFeeRate`;
- it **drops `wager`**. The persisted construction does not carry it.

So `/quote` returns 13 fields; a persisted `bet.construction` has 16.

{% hint style="warning" %}
**`bet.construction.wager` does not exist.** Read the wager from the bet's own top-level
field — `bet.wager`. Code that reads it from the construction works against a `/quote`
response and silently yields `undefined` against a stored bet.
{% endhint %}

{% hint style="info" %}
**Two different time formats in the same object.** `createdAt` and `updatedAt` are **epoch
milliseconds** (numbers). `proposalExpiresAt`, `activatedAt`, and `resolvedAt` are **ISO
strings**. Parse each accordingly.

Prefer `resolvedAt` over `updatedAt` when you need the moment a bet resolved — `updatedAt`
moves on every write.
{% endhint %}

{% hint style="warning" %}
**`accountId`, `relayClaimedAt` and `deletedAt` are internal.** They are listed above
because the API currently returns them, not because they are part of the contract —
`accountId` is Bound's own account key, `relayClaimedAt` belongs to the relay-once
mechanism, and `deletedAt` is a soft-delete marker. All three may stop being serialized
without notice. Do not build on them.
{% endhint %}

**Example:**

```jsonc
{
  "state": "active", "coin": "BTC", "wager": "20",
  "construction": { "leverage": 17, "takeProfitPrice": "63500", "liquidationPrice": "58656.39" },
  "venueRefs":    { "entryOid": "55606…", "takeProfitOid": "55607…", "closeOid": null },
  "actuals":      { "actualEntryPrice": "60758.2", "finalProceeds": null, "netProceeds": null }
}
```

{% hint style="warning" %}
**`finalProceeds` is gross, not net.** It is the sum of closed PnL, before fees and funding — never display it as the user's result.

**`netProceeds` is the authoritative net** (`finalProceeds` − all fees + funding), but it is **best-effort and nullable**. Bound writes it only when every input is trustworthy. When it is `null`, recompute client-side from Hyperliquid `userFills` (sum `closedPnl − fee − builderFee`) plus `userFunding`.
{% endhint %}

Two distinct "not found" shapes: an `id` that is not 24-hex does not match the route at all and returns a plain `404` with no wrapper, while a well-formed but unknown or foreign id returns the normal wrapper with `26001` or `26002`.

**Errors:** `26001` bet not found · `26002` ownership mismatch

---

## 11. POST `/api/which-first/withdraw` — move USDC back to HyperEVM

Returns an unsigned `sendAsset` action — the deposit's mirror. The funds are credited to the **signing wallet itself** on HyperEVM.

{% openapi src="https://api.bound.exchange/api/docs/openapi-bound.yaml" path="/api/which-first/withdraw" method="post" %}
https://api.bound.exchange/api/docs/openapi-bound.yaml
{% endopenapi %}


**Response data:**

```ts
type WithdrawAction = { kind: 'withdraw'; action: HlAction };
```

**Example:**

```ts
const w = await api<WithdrawAction>('/api/which-first/withdraw', {
  method: 'POST', jwt,
  body: JSON.stringify({ userAddress: '0xc12b…', amount: '10' }),
});
```

```jsonc
{
  "kind": "withdraw",
  "action": {
    "type": "sendAsset",
    "hyperliquidChain": "Mainnet",
    "signatureChainId": "0xa4b1",
    "destination": "0x2000000000000000000000000000000000000000", // USDC's system address — NOT a wallet
    "sourceDex": "spot",
    "destinationDex": "spot",
    "token": "USDC:0xeb62eee3685fc4c43992febcd9e75443",
    "amount": "10",
    "fromSubAccount": ""
  }
}
```

{% hint style="danger" %}
**Sign it like `approveBuilderFee`, not like an order.** `sendAsset` is a **user-signed EIP-712** action — use `signUserSignedAction` with the `HyperliquidTransaction:SendAsset` types, not `signL1Action`.

Unlike other user-signed actions, `sendAsset` signs over **`nonce` inside the action**. Add `nonce` (ms) to the action object, sign, then POST `{ action, nonce, signature }` — same nonce value — directly to Hyperliquid `/exchange`.

**There is no destination parameter.** The system-address path always credits the signing wallet itself; a third-party recipient is impossible here.
{% endhint %}

Confirm arrival via the wallet's USDC ERC-20 balance on HyperEVM.

**Errors:** `26021` invalid amount · `26016` no eligible EVM wallet

---

## Signing reference

Bound returns `{ kind, action }`. The `kind` tells you which of the two signing schemes the
action needs — dispatch on it, never on your own bookkeeping.

| `kind` | `action.type` | Scheme | Sign with |
| --- | --- | --- | --- |
| `update_leverage`, `entry`, `cancel`, `cash_out_close` | `updateLeverage` / `order` / `cancel` | L1 | `signL1Action` |
| `set_referrer` | `setReferrer` | L1 | `signL1Action` |
| `approve_builder_fee`, `withdraw` | `approveBuilderFee` / `sendAsset` | user-signed EIP-712 | `signUserSignedAction` |

```ts
import { signL1Action, signUserSignedAction } from '@nktkas/hyperliquid/signing';

// L1 — nonce increments per action
const signature = await signL1Action({ wallet, action, nonce, isTestnet: false });

// user-signed — add nonce INTO the action first; the SDK builds the domain
const signedAction = { ...action, nonce: Date.now() };
const signature2 = await signUserSignedAction({ wallet, action: signedAction, types });
```

Signature chain ids: mainnet `0xa4b1`, testnet `0x66eee`.

---

## Recovery patterns

Four situations every bet flow has to handle. Get these wrong and you either strand the
user or open a second live position.

### A setup gate fires on `/proposals`

`/referral` and `/builder-fee-approval` are one-time per wallet, so a bet flow normally
skips them. When it skips wrongly, `/proposals` is where you find out.

| Code | Do this |
| --- | --- |
| `26009` builder fee not approved | Call `/builder-fee-approval`, sign it, POST to Hyperliquid, then **re-propose once**. |
| `26020` referral not set | Call `/referral`, sign it, POST to Hyperliquid, then **re-propose once**. |

Re-propose **once**. A second identical failure is a real problem — surface it rather than
looping.

You can avoid most of these round-trips by reading the wallet's current state from
Hyperliquid's public `/info` endpoint before calling either setup endpoint, and skipping
the call when it is already done.

### The proposal expires mid-signing

`proposalExpiresAt` is short, and each signature costs a wallet round-trip. Two signatures
plus a slow user is enough to blow past it.

**Re-check the expiry before every signature, not just once after `/proposals`.** Relaying
an expired proposal means the fill lands on a bet the monitor has already aborted — an
untracked position on the user's account.

```ts
for (const unsigned of proposal.unsignedActions) {
  if (Date.parse(proposal.proposalExpiresAt) <= Date.now()) throw new Error('Quote expired — re-quote');
  const nonce = nextNonce();
  signed.push({ kind: unsigned.kind, action: unsigned.action, nonce, signature: await signL1Action({ wallet, action: unsigned.action, nonce, isTestnet }) });
}
```

### `/execute` returns `26028` or `26014`

{% hint style="danger" %}
**Neither is a failure — both mean "outcome unknown".** The bet exists. Treat them as
*processing*: show a neutral state, poll `GET /{id}` for the recorded truth, and let the
monitor settle it.

**Never re-sign and never resend.** A resend signs a fresh nonce, so Hyperliquid's own
deduplication cannot catch it — you would open a second live position.

`26025` is the opposite case: it is the one definitive "nothing landed" verdict, so
retrying after it is safe.
{% endhint %}

### The bet returns nothing to sign

If `unsignedActions` comes back empty, stop — do not treat it as success. Re-quote and
propose again.

---

## Realtime data

Bound stores each bet's **static identity, targets, and recorded outcome**. Anything that moves tick by tick comes **directly from Hyperliquid over WebSocket** — `wss://api.hyperliquid.xyz/ws`. That is the design: **live price and P&L come from Hyperliquid, not from Bound.**

| Data | Source | How |
| --- | --- | --- |
| Identity, bounds, side, leverage, take-profit and liquidation **targets**, payout estimate, `state` | **Bound** | `GET /:id` |
| Recorded outcome — `actuals.*` after resolution | **Bound** | `GET /:id` (written ≤30s after the venue event) |
| Live mark price | **Hyperliquid** | subscribe `allMids` |
| Live position P&L, live liquidation price, size | **Hyperliquid** | subscribe `webData2` |
| The fill the instant it happens | **Hyperliquid** | subscribe `userFills` / `orderUpdates` |

**Never poll Bound for P&L.** Compose the position card from Bound's static targets overlaid with Hyperliquid's live values.

For a pre-trade "potential return", drive a **debounced** `POST /quote` (~1s, or on bound change) from the live mark. Keep Bound as the single source of the leverage and payout math — do not recompute it client-side.

---

## Validation rules

- **Bounds** must satisfy `upper > mark > lower`. The **losing** bound — the one opposite your bet — must clear the per-side floor from `GET /limits`. Both bounds must be within `maxBoundDistance` (10%).
  - `upper_first` (LONG): take-profit is the upper bound, liquidation is the **lower** bound.
  - `lower_first` (SHORT): take-profit is the lower bound, liquidation is the **upper** bound.
- **Builder fee** must be approved once before the first bet.
- **Proposal expiry** — sign and execute before `proposalExpiresAt`. Do **not** sign an expired proposal's actions: a late fill becomes an untracked position. An expired proposal is aborted by the monitor.
- **One open bet per asset per account**, including across different wallets on the same account. A different asset is fine.
- **`maxSlippage`** applies to the entry and cash-out market orders only, never the take-profit trigger.
- **HIP-3 assets** keep their DEX prefix (`xyz:TSLA`). They additionally require the prefix to be trusted and reject non-USDC collateral.

---

## Error codes

All Barrier Perps errors use the `26xxx` range and arrive in the standard wrapper as `{ code, message, details? }`.

| Code | Name | Meaning |
| --- | --- | --- |
| `26001` | betNotFound | No such bet |
| `26002` | ownershipMismatch | The bet belongs to another account |
| `26003` | invalidBounds | Upper must be above the current price and lower below it |
| `26004` | boundCapExceeded | A bound is further from the mark than `maxBoundDistance` |
| `26005` | againstDistanceTooSmall | The liquidation-side bound is closer than the minimum |
| `26006` | wagerOutOfRange | Wager outside the configured range |
| `26007` | existingExposure | The account already has an open bet on this asset |
| `26008` | insufficientBalance | Not enough USDC on Hyperliquid |
| `26009` | builderFeeNotApproved | Approve the builder fee first |
| `26010` | priceNotFresh | Mark price is stale — re-quote |
| `26011` | proposalExpired | Sign and execute before `proposalExpiresAt` |
| `26012` | notActive | The bet is not active |
| `26013` | constructionFailed | Could not build the position |
| `26014` | venueUnavailable | Hyperliquid was unreachable — safe to retry. **On `/execute` only** it carries a second, non-retryable meaning: a money leg landed with no order id (see the execute guards) |
| `26016` | evmWalletNotFound | No eligible EVM wallet on the account |
| `26017` | portfolioMarginUnsupported | Portfolio Margin accounts are not supported |
| `26018` | invalidDepositAmount | Malformed or zero deposit amount |
| `26019` | notProposed | The bet is no longer awaiting signature |
| `26020` | referralNotSet | Referral required but not set |
| `26021` | invalidWithdrawAmount | Malformed or zero withdrawal amount |
| `26022` | unsupportedAsset | Asset not supported |
| `26023` | invalidSignedActionBundle | The signed bundle is structurally invalid |
| `26025` | orderRejected | Hyperliquid rejected the order — the message carries the reason |
| `26027` | actionMismatch | The signed actions do not match what Bound built |
| `26028` | alreadyExecuted | Relay-once guard — see the guards under the execute endpoint |
| `26029` | hip3ExecutionDisabled | HIP-3 execution is disabled |
| `26030` | insufficientLiquidity | The book cannot fill within the slippage limit |
| `26031` | unsupportedCollateral | This market's collateral is not supported |
| `26032` | orderNotionalTooSmall | Each order wire needs at least $10 notional |

`26015`, `26024`, and `26026` are retired and must not be reused.
