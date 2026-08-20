# Hedging Model

The Bound contract is the counterparty to every Boundary Perp. It does not create an independent Hyperliquid position for each user. Instead, it combines active positions into a per-underlying net book and hedges the resulting exposure.

## Position Delta

For the simplified linear model, a position's approximate hedge delta is:

```text
absolute hedge delta = payout / (U - L)
```

The sign depends on the chosen outcome:

* Upper-first positions create positive delta.
* Lower-first positions create negative delta.

The book target for one underlying is:

```text
net book delta = sum of signed active-position deltas
```

The contract holds that net exposure in the underlying's Hyperliquid perpetual. Exposure never nets across different underlyings.

This linear delta is an illustrative launch model. The core requirement is that the protocol dynamically derives and hedges each underlying's net book exposure.

## Example 1: Clean Hedge

Assume BTC has:

* Mark price: $100,000
* Upper boundary: $110,000
* Lower boundary: $97,500
* Position: upper first
* Stake: 1,000 USDC
* Gross payout before costs: 5,000 USDC

The hedge delta is:

```text
5,000 / (110,000 - 97,500) = +0.4 BTC
```

If BTC reaches $110,000 first:

```text
Contract position result: keep 1,000 stake, pay 5,000 = -4,000
Hedge result: 0.4 * (110,000 - 100,000) = +4,000
Net before costs: 0
```

If BTC reaches $97,500 first:

```text
Contract position result: keep 1,000 stake = +1,000
Hedge result: 0.4 * (97,500 - 100,000) = -1,000
Net before costs: 0
```

The hedge makes the contract approximately indifferent to the first-touch outcome. Fees and spread compensate the protocol and liquidity pool for costs and risk.

## Example 2: Netted Exposure

Suppose the book already contains the `+0.4 BTC` position above. A second user opens:

* Mark price: $100,000
* Upper boundary: $105,000
* Lower boundary: $95,000
* Position: lower first
* Stake: 2,500 USDC
* Gross payout before costs: 5,000 USDC

The second position contributes:

```text
5,000 / (105,000 - 95,000) = -0.5 BTC
```

The combined target becomes:

```text
+0.4 BTC - 0.5 BTC = -0.1 BTC
```

Independent hedges would require `0.9 BTC` of gross perpetual exposure. Netting requires only `0.1 BTC`, reducing margin, funding, and execution requirements.

## Dynamic Rebalancing

Net exposure changes when:

* A position opens.
* A position settles.
* A position closes early.
* The mark changes the position's probability and delta.

Positions with different boundaries do not offset perfectly across all prices. If one position resolves, its delta disappears from the book immediately, potentially causing a large target change. The protocol therefore recalculates and rebalances the hedge after every state-changing action and during ongoing market movement.

## Hedge Execution Controls

The hedge execution design limits losses from manipulated or adverse fills:

* **Mark-pegged limit orders:** Orders must remain within `max_hedge_slippage` of the current mark.
* **Tranche cap:** A large target change is divided into orders no larger than `max_rebalance_tranche`.
* **Turnover cap:** Hedge notional traded during a configured window cannot exceed `max_hedge_turnover`.
* **Spread relationship:** The base spread is configured above the maximum permitted hedge slippage so repeatedly opening and closing positions cannot profitably force hedge churn under expected conditions.

The approximate maximum slippage loss per turnover window is:

```text
max_hedge_slippage * max_hedge_turnover
```

If the hedge cannot track its target within the configured deviation band and persistence window, the hedge-staleness circuit breaker restricts activity.
