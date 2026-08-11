# Circuit Breakers

Circuit breakers restrict protocol actions when leverage, solvency, hedge execution, infrastructure, price, or liquidity conditions move outside configured limits.

Every breaker defines:

* A trigger
* A response mode
* The actions allowed while active
* A manual or automatic resume condition

Breaker state is evaluated on every state-changing call and keeper heartbeat. A quote issued before a breaker trips can still be rejected during acceptance.

## Response Modes

### Restricted

Only actions that are safe under the specific condition or improve the affected metric are allowed.

### Freeze

New quotes, settlement, claims, early closes, LP withdrawals, fee collection, and other outflows stop. LP deposits and mark-worker publications remain available because they add capital or preserve settlement evidence.

## Breaker Matrix

| # | Breaker | Trigger | Mode | Primary permitted actions | Resume |
| --- | --- | --- | --- | --- | --- |
| 1 | Hedge leverage | Book leverage exceeds `max_book_leverage` | Restricted | Opens and buybacks that reduce net exposure | Automatic below configured resume level |
| 2 | Solvency floor | `book_assets` falls below `solvency_multiplier * marked_liability` | Restricted | Buybacks only | Automatic above configured resume level |
| 3 | Hedge staleness | Target and actual hedge remain outside the deviation band longer than the persistence window | Restricted | Actions that move the target toward the actual hedge | Automatic when deviation returns inside the band |
| 4 | Precompile unavailable | HyperCore precompile cannot be read | Restricted | Claims only | Automatic after the required clean checks |
| 5 | Mark shock | Mark change exceeds the configured threshold within its window | Freeze | LP deposits and mark-worker publications | Automatic after the required clean checks |
| 6 | Buffer drawdown | NAV per share falls beyond the drawdown limit within its window | Restricted | Actions that do not consume the outflow allowance | Automatic as the trailing window recovers |
| 7 | Admin pause | Authorized manual action | Freeze | LP deposits and mark-worker publications | Authorized manual action |
| 8 | Mark-worker staleness | No stored reading within the allowed publish intervals | Restricted | Claims only | Automatic after the required clean checks |

## Breaker 1: Hedge Leverage

```text
book leverage = hedge_notional / core_equity
```

`hedge_notional` is the sum of the absolute notional of the contract's per-underlying net hedge positions. `core_equity` is the HyperCore account equity, including posted collateral and unrealized hedge profit or loss.

Only actions that reduce net exposure are accepted while the breaker is active.

## Breaker 2: Solvency Floor

```text
book_assets < solvency_multiplier * marked_liability
```

The multiplier covers residual risk not captured by the position-marking model, including gaps between checks, marking error, and hedge variance.

Only buybacks are allowed because they retire a position below its marked liability and improve coverage. New opens are refused even when they would reduce net exposure.

## Breaker 3: Hedge Staleness

```text
D = absolute value of (target net delta - actual hedge delta)
```

The breaker trips when `D` remains above deviation band `B` for longer than persistence window `N`. Returning inside the band clears the timer.

An action qualifies only if it moves the target toward the actual hedge. Merely reducing net exposure is not sufficient if it increases the target-to-actual deviation.

## Breaker 4: Precompile Unavailable

If the contract cannot read the HyperCore precompile, it cannot safely quote, open, settle, value the book, or adjust its hedge. Claims remain available because a resolved payout is fixed and does not require market data.

During this condition, outflow capacity is measured against USDC held directly by the contract because HyperCore-side assets cannot be read.

## Breaker 5: Mark Shock

The contract compares stored mark checkpoints and trips if the move exceeds `mark_jump_threshold` within `mark_jump_window`.

This is a Freeze condition because a shock can invalidate normal pricing, marking, and hedge assumptions.

## Breaker 6: Buffer Drawdown

The protocol tracks NAV per share between stored checkpoints. Under ordinary hedged movement, hedge profit and loss should offset changes in marked liabilities. A large NAV decline can indicate hedge failure, extreme costs, or an unrecognized liability.

While this breaker is active, claims, buyback quotes, LP withdrawals, and fee sweeps are blocked because they consume outflow capacity. Quoting and opening can continue when otherwise safe.

## Breaker 7: Admin Pause

An authorized administrator may place the book into Freeze. Resumption also requires an authorized call.

## Breaker 8: Mark-Worker Staleness

The contract measures the time since the most recent stored mark reading. If the mark worker misses `X` consecutive publish intervals, quoting, opening, and settlement stop.

An in-call reading alone cannot safely establish first-touch order when the historical observation record has a gap. Claims remain available because they relate to positions already resolved.

## Combined Breakers

When multiple breakers are active, the strictest response applies. An action must satisfy every active restriction.

For example, if the solvency-floor and buffer-drawdown breakers are active together, the combined restrictions can produce a temporary standstill in which only settlement, LP deposits, and mark-worker publications remain available.

## LP and Settlement Rules

* LP withdrawals are blocked while any breaker is active.
* LP deposits remain available in every mode.
* Mark-worker publications remain available in every mode.
* Settlement is allowed under Restricted modes except mark-worker staleness.
* Claims are allowed under Restricted modes except buffer drawdown.
* Settlement and claims both halt during Freeze.
* Automatic resume thresholds sit beyond the trip boundary to prevent rapid cycling.
