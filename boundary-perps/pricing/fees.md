# Fees

Boundaries have two types of fees: an **Upfront Fee** taken from your USDC stake when you open, and a **Spread** built into the payoff multiple you are offered. The LP fee part of the Spread is only collected when you make a profit.

| Fee | What it is | What it includes |
| --- | --- | --- |
| **Upfront Fee** | Taken from your USDC stake at open | Protocol fee (1% of stake) + Hyperliquid execution fee and slippage for opening the hedge |
| **Spread** | Built into the payoff multiple shown in your quote | Skew + estimated funding + estimated slippage and Hyperliquid execution fee for closing the hedge + LP fee |
| **Early-close fee** | Taken when you close early | 1% of the amount you receive |

Rates are protocol settings and can change. A change applies only to new positions: an open position keeps the terms it had when it opened.

## Upfront Fee

The Upfront Fee is taken from your stake when the position opens. It covers:

* **Protocol fee:** 1% of your stake.
* **Hyperliquid execution fee:** the taker fee on the market order that opens the hedge behind your position (4.5 bp of the hedge notional).
* **Slippage:** the price impact of that opening order. It grows with hedge size.

The rest of your stake is the amount your position is priced on. The fee is shown before you confirm.

## Spread

The Spread is not charged as a separate line. It is already included in the payoff multiple your quote shows, so a quoted 2× is what you receive at target. It includes:

* **Skew:** a charge (or rebate) depending on whether your position adds to or balances the book's exposure.
* **Estimated funding:** the expected funding cost of holding the hedge.
* **Estimated slippage and Hyperliquid execution fee for closing the hedge:** the expected cost of unwinding the hedge when your position ends. You are not charged it again later.
* **LP fee:** the liquidity pool's margin.

**The LP fee only applies to profit.** If you win at target, it is already in your quoted payoff. If you close early in profit, it is scaled to the profit you have so far. If you lose, or close early at a loss, no LP fee is collected. It is never more than 50% of your profit.

## Closing early

Your position's value at any moment is its payout times the current chance of hitting your target first. When you close early:

1. If you are in profit, the matching part of the LP fee is deducted.
2. The 1% early-close fee is deducted from what is left.

The Current Value shown in the app is the amount you receive after both.

## Example

$20 on BTC Up, stop 5% below, target 5% above (a fair 2× bet), using today's rates.

| Step | Amount |
| --- | --- |
| Stake after 1% protocol fee | $19.80 |
| Payout after hedge costs | $38.74 |
| LP fee at target | $3.58 |
| **You receive if BTC hits the target** | **$35.16** |

Closing early, same position:

| Price | Value | LP fee | You receive (after 1% fee) |
| --- | --- | --- | --- |
| Unchanged since open | $19.37 | $0 | $19.18 |
| Halfway to target | $29.05 | $1.67 | $27.11 |
| Halfway to stop | $9.69 | $0 | $9.59 |
| Stop hit first | $0 | $0 | $0 |
