# How Boundary Perps Work

Every Boundary Perp follows the same basic flow, while each product defines its own outcome and pricing model.

## 1. Configure a Position

Select a Boundary Perps product and its underlying asset, then configure the product-specific parameters.

For a Vanilla Boundary Perp on a given asset, you select:

* An upper price boundary
* A lower price boundary
* Which boundary you believe will be reached first
* A USDC stake
* A minimum acceptable payoff (slippage)

## 2. Request a Quote

Bound validates the request and calculates an indicative quote using current market data, the product's pricing model, expected hedging costs, and the existing exposure of the book.

The quote displays:

* Your stake
* The protocol fee
* The fixed payout if your position wins
* The quote's validity period

Quotes expire because market prices and hedging conditions change continuously.

## 3. Review and Accept

When you accept, the contract validates the position again and recomputes the payout using the latest state. You provide a minimum acceptable payout so the transaction reverts if the updated payout is below what you are willing to accept.

If acceptance succeeds:

* Your stake and protocol fee transfer to the contract.
* Your boundaries and fixed payout are locked.
* The position becomes **Active**.

## 4. Bound Manages the Book

The contract combines active positions into a net book for each underlying asset. It uses the position stakes and a liquidity buffer as collateral while hedging the book's net market exposure on Hyperliquid.

This hedging happens at the protocol level. Your position remains a fixed-payout Boundary Perp.

## 5. The Position Resolves

The product's settlement condition determines the outcome. For Vanilla Boundary Perps, the position resolves when one of the two boundaries is observed crossing first.

* If your chosen boundary crosses first, the position is **Resolved — Won**.
* If the other boundary crosses first, the position is **Resolved — Lost**.

## 6. Claim or Close Early

After a win, the fixed payout becomes a liability owed to the position owner and is claimable under normal solvency conditions. A last-resort socialized-loss adjustment may apply if the book cannot cover its liabilities.

Before either outcome occurs, you may also request a buyback quote to close the position early. Accepting it closes the position for the quoted amount. Declining it leaves the position active.
