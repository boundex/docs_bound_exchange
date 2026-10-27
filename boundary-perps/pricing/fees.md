# Fees

Every position has an **Upfront Fee**, taken from your stake at open, and a **Spread**, built into the payoff multiple and only collected when you profit. Closing early adds a 1% **early-close fee**.

![How fees apply across a position's life](../../.gitbook/assets/fees-cycle.svg)

| Category | When | What it includes |
| --- | --- | --- |
| **Upfront Fee** | Taken from your USDC stake at open | Protocol fee + Hyperliquid execution fee for opening and closing the hedge |
| **Spread** | Built into the payoff multiple shown in your quote | Skew + estimated funding + estimated slippage for opening and closing the hedge + LP fee |
| **Early-close fee** | Taken when you close early | 1% of the amount paid out |

Rates can change over time. Changes only apply to new positions. Open positions keep the rates they were opened with.

## Upfront Fee

The Upfront Fee is taken from your stake when you open a position. It includes:

* **Protocol fee:** 1% of your stake.
* **Hyperliquid execution fee:** Hyperliquid's taker fee on the hedge size, charged twice (once to open the hedge, once to close it). The rate depends on the asset; see [Hyperliquid fees](https://hyperliquid.gitbook.io/hyperliquid-docs/trading/fees). Assets in Hyperliquid's [growth mode](https://hyperliquid.gitbook.io/hyperliquid-docs/trading/fees#growth-mode-for-hip-3-perps), such as TSLA, HOOD, COIN and SP500, have a much lower fee.

Your payout is calculated on the remaining stake.

## Spread

The Spread is built into the payoff multiple of your quote. It includes:

* **Skew:** an extra charge if your position adds to the book's imbalance, or a discount if it reduces it.
* **Estimated funding:** the expected funding cost of the hedge.
* **Estimated slippage:** for opening and closing the hedge. Larger hedges have more slippage.
* **LP fee:** a fee paid to the liquidity pool.

The Spread is only collected when you profit. If you lose, no Spread is charged.

## Early Close

What you pay when closing early depends on whether your position is in profit:

| Your position | What you pay |
| --- | --- |
| In profit | Spread × (your profit now ÷ your profit at target before Spread) + early-close fee |
| At a loss or break-even | Early-close fee only |

For example, if your profit is halfway to your profit at target, you pay half the Spread. The early-close fee is 1%.

Example: $100 stake, $20 Spread. Price is halfway from entry to target, and you close early. The $2 Upfront Fee is an example amount. The Hyperliquid execution fee depends on the size of the hedge.

| Step | Amount |
| --- | --- |
| Stake | $100.00 |
| Upfront Fee (example: $1 protocol fee + $1 Hyperliquid execution fee) | −$2.00 |
| Stake after Upfront Fee | $98.00 |
| Implied value at target ($176 payout + $20 Spread) | $196.00 |
| Implied value now ($196 × 75%) | $147.00 |
| Profit now ($147 × 99% − $98) | $47.53 |
| Spread share ($20 × $47.53 ÷ $98) | −$9.70 |
| Early-close fee (1% of $137.30) | −$1.37 |
| **You receive** | **$135.93** |

Current Value in the app is your implied value minus the Spread share. Closing early pays Current Value minus the 1% early-close fee. Right after you open, Current Value is your stake minus the Upfront Fee.
