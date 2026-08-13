# Barrier Perps

Barrier Perps are fixed-payout derivatives whose outcome depends on a price barrier event. Instead of predicting an exact future price, you choose a market outcome with clearly defined conditions, a maximum loss, and a payout that is fixed when the position opens.

The first product in this family is **Vanilla Barrier Perps**. It lets you choose two price barriers around the current market price and take a position on which barrier will be reached first.

## At a Glance

With a Barrier Perp, you:

1. Select a product and underlying asset.
2. Configure its barriers and choose an outcome.
3. Enter the amount of USDC you want to stake.
4. Review a quote showing the fixed payout and fee.
5. Accept the quote to open the position.

Your maximum loss is the stake committed to the position. If your selected outcome occurs, the fixed payout shown when the position opened becomes the book's liability to you. If the other outcome occurs, you lose the stake.

{% hint style="info" %}
**Payout includes your returned stake.** Net profit is the payout minus your original stake and any fees.
{% endhint %}

## Example

Assume BTC is trading at **$100,000**. You configure a Vanilla Barrier Perp with:

| Parameter | Selection |
| --- | --- |
| Upper barrier | $110,000 |
| Lower barrier | $97,500 |
| Position | Upper barrier first |
| Stake | 1,000 USDC |

Bound returns a quote with a fixed payout. If you accept it and BTC reaches $110,000 before $97,500, the position resolves as won and the payout becomes claimable. If BTC reaches $97,500 first, the position resolves as lost and the stake is lost.

## Key Characteristics

* **Defined maximum loss:** You cannot lose more than your stake on the position.
* **Fixed payout:** The payout is locked when the position opens, subject to the disclosed last-resort insolvency mechanism.
* **Flexible expression:** Products define clear market outcomes without requiring a prediction of the final price at a specific time.
* **Early close:** Active positions may be closed early by accepting a new buyback quote.
* **Onchain settlement:** Positions settle through the Bound contract on HyperEVM using Hyperliquid market data.
* **Permissionless claims:** Once a winning position settles, its payout can be claimed to the position owner.

Continue to [How Barrier Perps Work](how-it-works.md) for the complete position flow, or see [Vanilla Barrier Perps](products/vanilla-barrier-perps.md) for the first product.
