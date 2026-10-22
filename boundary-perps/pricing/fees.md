# Fees

Boundaries have three fee categories: an **Upfront Fee** taken from your USDC stake when you open, a **Spread** built into the payoff multiple you are offered, and an **Early Close** fee that applies only if you close before the position resolves.

| Category | When | What it includes |
| --- | --- | --- |
| **Upfront Fee** | Taken from your USDC stake at open | Protocol fee + Hyperliquid execution fee for opening and closing the hedge |
| **Spread** | Built into the payoff multiple shown in your quote | Skew + estimated funding + LP fee + estimated slippage for opening and closing the hedge |
| **Early Close** | Taken when you close early | Your share of the Spread so far (only if in profit) + early-close fee |

Rates are protocol settings and can change. A change applies only to new positions: an open position keeps the terms it had when it opened.

## Upfront Fee

The Upfront Fee is taken from your stake when the position opens. It covers:

* **Protocol fee:** 1% of your stake.
* **Hyperliquid execution fee:** the taker fee on the two market orders that open and later close the hedge behind your position (4.5 bp of the hedge notional each).

The rest of your stake is the amount your position is priced on. The fee is shown before you confirm.

## Spread

The Spread is not charged as a separate line. It is already included in the payoff multiple your quote shows, so a quoted 2× is what you receive at target. It includes:

* **Skew:** a charge (or rebate) depending on whether your position adds to or balances the book's exposure.
* **Estimated funding:** the expected funding cost of holding the hedge.
* **LP fee:** the liquidity pool's margin.
* **Estimated slippage:** the expected price impact of opening and closing the hedge. It grows with hedge size.

If you win at target, you receive the quoted payoff, with the Spread already taken out. If you lose, there is nothing to pay out and no Spread is collected.

## Early Close

When you close early, the fee depends on whether you are in profit:

| Your position | Early Close fee |
| --- | --- |
| In profit | Spread × (your profit now ÷ your profit at target) + early-close fee |
| At a loss or break-even | Early-close fee only |

In profit, you pay the share of the Spread you have earned so far: halfway to your target profit means half the Spread. The early-close fee is 1% of the amount you receive.

Your position's value at any moment is its payout times the current chance of hitting your target before your stop. The Current Value shown in the app is the amount you receive after the Early Close fee.

## Example

$20 on BTC Up, stop 5% below, target 5% above (a fair 2× bet). Uses BTC's current settings: 4.5 bp Hyperliquid taker fee, 10.5% yearly funding, 1.6% daily volatility, a balanced book (no skew), and an LP fee of 3.5% at 2×.

1. **Stake:** $20.00
2. **Upfront Fee:** −$0.55 ($0.20 protocol fee + $0.35 Hyperliquid execution on a $389 hedge, open and close)
3. **Priced stake:** $19.45
4. **Fair payout at 2×:** $38.90
5. **Spread at target** (LP fee, funding, slippage): −$3.71
6. **You receive if BTC hits the target (5% above) before the stop: $35.19**

Closing early, same position:

| Price | Value | Spread share | You receive (after 1% early-close fee) |
| --- | --- | --- | --- |
| Unchanged since open | $19.45 | $0 | $19.26 |
| Halfway to target | $29.17 | $1.75 | $27.16 |
| Halfway to stop | $9.72 | $0 | $9.63 |
| Stop hit first | $0 | $0 | $0 |

_Illustrative. Live quotes can differ by a few cents with market conditions and book exposure._
