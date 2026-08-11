# How Barrier Perps Work

Every Barrier Perp follows the same basic flow, while each product defines its own outcome and pricing model.

## 1. Configure a Position

Select a Barrier Perps product and its underlying asset, then configure the product-specific parameters.

For a Vanilla Barrier Perp, you select:

* An upper price barrier
* A lower price barrier
* Which barrier you believe will be reached first
* A USDC stake

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
* Your barriers and fixed payout are locked.
* The position becomes **Active**.

## 4. Bound Manages the Book

The contract combines active positions into a net book for each underlying asset. It uses the position stakes and a liquidity buffer as collateral while hedging the book's net market exposure on Hyperliquid.

This hedging happens at the protocol level. Your position remains a fixed-payout Barrier Perp and does not become a perpetual futures position in your personal Hyperliquid account.

## 5. The Position Resolves

The product's settlement condition determines the outcome. For Vanilla Barrier Perps, the position resolves when one of the two barriers is observed crossing first.

* If your chosen barrier crosses first, the position is **Resolved: Won**.
* If the other barrier crosses first, the position is **Resolved: Lost**.

## 6. Claim or Close Early

After a win, the fixed payout becomes claimable to the position owner.

Before either outcome occurs, you may also request a buyback quote to close the position early. Accepting it closes the position for the quoted amount. Declining it leaves the position active.
