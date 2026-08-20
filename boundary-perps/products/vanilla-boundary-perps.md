# Vanilla Boundary Perps

Vanilla Boundary Perps let you take a position on which of two price boundaries an asset will reach first.

## Defining the Position

The current market price must sit strictly between two boundaries:

* **Upper bound:** A price above the current market price
* **Lower bound:** A price below the current market price

You then choose one of two outcomes:

* **Upper first:** The upper bound will be reached before the lower bound.
* **Lower first:** The lower bound will be reached before the upper bound.

The boundary you select is the **chosen boundary**. The other is the **non-chosen boundary**.

## Outcomes

| First boundary crossed | Upper-first position | Lower-first position |
| ---------------------- | -------------------- | -------------------- |
| Upper bound            | Won                  | Lost                 |
| Lower bound            | Lost                 | Won                  |

A boundary counts as crossed when the settlement price reaches or passes it. The market does not need to stop or trade continuously at the exact boundary value.

## Example

BTC is trading at **$100,000**. You choose:

* Upper boundary: **$110,000**
* Lower boundary: **$97,500**
* Outcome: **Upper first**
* Stake: **1,000 USDC**

Because the upper boundary is farther from the current price than the lower boundary, upper-first is the less likely outcome under the pricing model and therefore offers a larger potential payout.

If the accepted quote shows a fixed payout of **5,000 USDC**:

* BTC reaching $110,000 first records a winning payout of 5,000 USDC
* BTC reaching $97,500 first loses the 1,000 USDC stake.

The 5,000 USDC payout includes the original 1,000 USDC stake, so the gross profit before fees is 4,000 USDC.

## Position Rules

* Both boundaries must be within the supported range for the asset.
* The non-chosen boundary must satisfy the configured minimum distance.
* The stake must be within the supported limits.
* A user may hold multiple positions on the same underlying, subject to available book capacity.
* Boundaries and payout cannot be changed after the position opens.
* An active position can resolve at a boundary or be [closed early](../closing-early.md).

See [Vanilla Boundary Perps Pricing](../pricing/vanilla-boundary-perps.md) to understand how boundary placement affects the quote.
