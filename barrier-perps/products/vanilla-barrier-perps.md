# Vanilla Barrier Perps

Vanilla Barrier Perps let you take a position on which of two price barriers an asset will reach first.

## Defining the Position

The current market price must sit strictly between two barriers:

* **Upper barrier:** A price above the current market price
* **Lower barrier:** A price below the current market price

You then choose one of two outcomes:

* **Upper first:** The upper barrier will be reached before the lower barrier.
* **Lower first:** The lower barrier will be reached before the upper barrier.

The barrier you select is the **chosen barrier**. The other is the **non-chosen barrier**.

## Outcomes

| First barrier crossed | Upper-first position | Lower-first position |
| --- | --- | --- |
| Upper barrier | Won | Lost |
| Lower barrier | Lost | Won |

A barrier counts as crossed when the settlement price reaches or passes it. The market does not need to stop or trade continuously at the exact barrier value.

## Example

BTC is trading at **$100,000**. You choose:

* Upper barrier: **$110,000**
* Lower barrier: **$97,500**
* Outcome: **Upper first**
* Stake: **1,000 USDC**

Because the upper barrier is farther from the current price than the lower barrier, upper-first is the less likely outcome under the pricing model and therefore offers a larger potential payout.

If the accepted quote shows a fixed payout of **5,000 USDC**:

* BTC reaching $110,000 first makes 5,000 USDC claimable.
* BTC reaching $97,500 first loses the 1,000 USDC stake.

The 5,000 USDC payout includes the original 1,000 USDC stake, so the gross profit before fees is 4,000 USDC.

## Position Rules

* Both barriers must be within the supported range for the asset.
* The non-chosen barrier must satisfy the configured minimum distance.
* The stake must be within the supported limits.
* A user may hold multiple positions on the same underlying, subject to available book capacity.
* Barriers and payout cannot be changed after the position opens.
* An active position can resolve at a barrier or be [closed early](../closing-early.md).

See [Vanilla Barrier Perps Pricing](../pricing/vanilla-barrier-perps.md) to understand how barrier placement affects the quote.
