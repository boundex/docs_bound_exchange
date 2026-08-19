# Pricing Model

This page describes the technical pricing model for Vanilla Barrier Perps. The accepted onchain quote remains the binding value for every position.

## Price Basis

All quoting, validation, position marking, and settlement use the underlying asset's HyperCore mark price.

Let:

* `S` be the current mark price.
* `U` be the upper barrier.
* `L` be the lower barrier.
* `p` be the probability of the chosen barrier crossing first.

Using the launch model's linear touch-probability approximation:

```text
Upper-first p = (S - L) / (U - L)
Lower-first p = (U - S) / (U - L)
```

The contract caps the probability used for marking to the interval `[0, 1]`.

## Fair Payout

Ignoring costs, the fair gross payout is:

```text
fair payout = stake / p
```

This is equivalent to pricing the position as a standalone perpetual that uses the stake as isolated margin, places liquidation at the non-chosen barrier, and places take profit at the chosen barrier. The replication is a pricing device only. The protocol hedges the net book rather than creating one hedge for every user position.

## Quoted Payout

The contract adjusts the stake for expected costs and pool margin before scaling by the chosen outcome's probability:

```text
quoted payout =
  (stake - funding_reserve - execution_reserve - spread) / p
```

The three deductions are collected in full regardless of which barrier resolves the position.

### Funding Reserve

The funding reserve estimates perpetual funding over the position's expected life. The expected duration uses:

```text
E[tau] approximately equals d_up * d_down / sigma^2
```

Where:

* `d_up` is the fractional distance from the mark to the upper barrier.
* `d_down` is the fractional distance from the mark to the lower barrier.
* `sigma` is the configured daily volatility for the asset.
* `tau` is measured in days.

The realized funding cost can differ from the estimate. The buffer absorbs that variance.

### Execution Reserve

The execution reserve estimates the trading fees and slippage required to establish and rebalance the protocol's Hyperliquid hedge. It also accounts for the brief latency between accepting a position and completing the related hedge adjustment.

### Spread

The pool's margin consists of a base spread and a signed imbalance adjustment:

```text
spread = base_spread + lambda * (I_post^2 - I_pre^2)
```

`I_pre` and `I_post` are the normalized book imbalance before and after the position:

```text
I = underlying net hedge notional / capacity
capacity = c * max_book_leverage * NAV
```

The base spread is calculated from the position's expected hedge notional:

```text
base_spread = base_spread_rate * hedge notional
hedge notional approximately equals stake * S / D
```

`D` is the distance from the mark to the non-chosen barrier. This stake-based form makes the spread computable before the payout it helps determine.

A position that increases the absolute imbalance pays a positive risk charge. A position that reduces imbalance receives a discount. The discount can exceed the base spread and protocol fee, so an exposure-reducing quote may be above the standalone fair payout. The model does not impose a floor of zero on the signed imbalance adjustment.

## Buyback Pricing

An early-close buyback begins with the position's current marked value:

```text
current value = current chosen-outcome probability * fixed payout
```

The same imbalance-priced spread function is applied to the buyback's change in book state. Across an open followed by a buyback, the signed imbalance charges offset, while the base spread is paid in both directions.

## Quote Acceptance

The initial quote is indicative. During acceptance, the contract:

1. Reads the current mark and book state.
2. Revalidates every position constraint.
3. Recomputes the binding payout.
4. Compares it with the user's `minPayout`.
5. Reverts if the new payout is below `minPayout`.

The accepted payout is fixed as the book's recorded liability for the life of the position. A last-resort socialized-loss adjustment can reduce the final amount paid if the book becomes insolvent.

## Leverage Padding

The protocol may optionally quote a position whose non-chosen barrier implies leverage above the venue limit. In that case, the LP buffer supplies additional hedge margin beyond the user's stake.

Leverage padding changes which barrier configurations are eligible. It does not change the payout formula. The amount of permitted padding is controlled by a configured cap and may be zero.
