# Pricing Overview

Barrier Perps use request-for-quote pricing. Each quote is calculated for one specific product configuration and returns a fixed payout that can be locked by accepting the quote.

## What Affects a Quote?

A quote can depend on:

* The selected product and its payoff
* The current Hyperliquid mark price
* The position's barriers and chosen outcome
* The USDC stake
* Expected funding and hedge execution costs
* The exposure already held by the book
* The protocol's spread

Different Barrier Perps products can use different pricing models. Each product therefore has its own page explaining how its inputs affect the payout.

## Fixed Payout

The displayed payout is the total amount paid on a win, including the returned stake. It is not the same as profit.

```text
Gross profit = fixed payout − stake
```

Fees should also be considered when assessing the net result.

The accepted payout is the book's fixed liability under normal operation. In an extreme insolvency, the final amount paid may be reduced by the protocol's [socialized-loss mechanism](../risks-and-safeguards.md#insolvency-and-reduced-payouts).

## Quote Expiration

Quotes are only valid for a short period because market prices, funding conditions, and book exposure can change.

When you accept a quote, the contract recomputes the payout using current conditions. Your transaction includes a **minimum payout**. If the new payout is lower than that amount, the transaction reverts and no position opens.

## Costs and Spread

The pricing model accounts for the expected cost of maintaining the protocol's hedge, including:

* Expected funding costs
* Expected trading fees and execution costs
* A spread earned by the liquidity pool

The spread may also reflect how the new position changes the book's net exposure. A position that offsets existing exposure can receive more favorable pricing than one that increases imbalance.

## Protocol Fee

A protocol fee is charged separately when a position opens. It is shown before acceptance and is paid in addition to the stake. Because it is separate, the full stake remains the basis for the position's payout and hedge calculations.
