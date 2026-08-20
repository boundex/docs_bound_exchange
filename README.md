# Overview

Boundary Perps are fixed-payout derivatives whose outcome depends on a price bound event. You choose a market outcome with clearly defined conditions, a maximum loss, and a payout that is fixed when the position opens.

The first product in this family is **Vanilla Boundary Perps**. It lets you choose two price boundaries around the current market price and take a position on which boundary will be reached first.

## At a Glance

With a Boundary Perp, you:

1. Select an underlying asset.
2. Configure its boundaries and choose an outcome.
3. Enter the amount of USDC you want to stake.
4. Review a quote showing the fixed payout and fee.
5. Accept the quote to open the position.

Your maximum loss is the stake committed to the position. If your selected outcome occurs, you earn the fixed payout shown when the position was opened becomes. If the other outcome occurs, you lose the stake.

{% hint style="info" %}
**Payout includes your returned stake.** Net profit is the payout minus your original stake and any fees.
{% endhint %}

## Example

Assume BTC is trading at **$100,000**. You configure a Vanilla Boundary Perp with:

| Parameter   | Selection         |
| ----------- | ----------------- |
| Upper bound | $110,000          |
| Lower bound | $97,500           |
| Position    | Upper bound first |
| Stake       | 1,000 USDC        |

Bound returns a quote with a fixed payout. If you accept it and BTC reaches $110,000 before $97,500, the position resolves as won and the payout becomes claimable. If BTC reaches $97,500 first, the position resolves as lost and the stake is lost.

## Key Characteristics

* **Defined maximum loss:** You cannot lose more than your stake on the position.
* **Fixed payout:** The payout is locked when the position opens, subject to the disclosed last-resort insolvency mechanism.
* **Flexible expression:** Products define clear market outcomes without requiring a prediction of the final price at a specific time.
* **Early close:** Active positions may be closed early by accepting a new buyback quote.
* **Onchain settlement:** Positions settle through the Bound contract on HyperEVM using Hyperliquid market data.
* **Decentralized Execution:** The entire position lifecycle is executed on HyperEVM and HyperCore via smart contracts without reliance on a trusted third-party

Continue to [How Boundary Perps Work](boundary-perps/how-it-works.md) for the complete position flow, or see [Vanilla Boundary Perps](boundary-perps/products/vanilla-boundary-perps.md) for the first product.
