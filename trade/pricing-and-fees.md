# Pricing And Fees

## AMM pricing

Bound's Runes AMM uses **Concentrated Liquidity** - a variation on the constant product formula (x\*y=k). When you request a quote, the AMM calculates the price based on current liquidity across all active positions in range.

## Fee tiers

Liquidity Providers choose from four fee tiers when creating a pool:

| Fee tier | Best for                       |
| -------- | ------------------------------ |
| 0.05%    | Stable pairs, low volatility   |
| 0.30%    | Standard trading pairs         |
| 0.50%    | Less liquid pairs              |
| 1.00%    | Exotic / high-volatility pairs |

## Fee distribution

For every trade, fees are split:

* **2/3 → Liquidity Providers** - claimable from the Portfolio tab
* **1/3 → Bound** - protocol revenue

## Quote validity

Quotes are valid at the moment of request. If another user's activity changes the pool state before you confirm, your quote may expire. You'll need to request a new quote and resubmit.

{% hint style="info" %}
**VERIFY** — Slippage tolerance settings and user controls during the swap flow to be confirmed with team.
{% endhint %}
