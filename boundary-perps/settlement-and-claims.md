# Settlement & Claims

Boundary Perps settle onchain according to the outcome rules of each product. Vanilla Boundary Perps use first-touch settlement: whichever boundary is observed crossing first determines the result.

## Settlement Price

The protocol uses the underlying asset's **HyperCore mark price** as its settlement price reference. The same price basis is used for quoting, validation, marking active positions, and settlement.

For Vanilla Boundary Perps:

* The upper boundary is crossed when the mark is greater than or equal to it.
* The lower boundary is crossed when the mark is less than or equal to it.

## Observing Boundary Crossings

A permissionless mark worker periodically publishes authenticated HyperCore mark readings onchain. These stored readings create a record the contract can use to establish which boundary crossed first.

A crossing recorded by the mark worker remains settleable even if the settlement transaction is submitted later. Very brief price movements that occur and reverse between observable readings may not be captured; the publish cadence therefore defines the protocol's observation floor.

## Permissionless Settlement

Settlement calls are permissionless. A keeper normally submits them promptly, but the protocol does not depend on one exclusive operator.

When a valid settle call is processed:

* A chosen-boundary crossing resolves the position as **Won**.
* A non-chosen-boundary crossing resolves the position as **Lost**.
* The resolved position leaves the active book.
* The protocol recalculates its net exposure and adjusts its hedge.

## Claims

After a win, the fixed payout becomes a liability of the contract and can be claimed to the position owner.

Claims are permissionless in the sense that settlement does not depend on Bound submitting the transaction. Calling the claim cannot redirect the payout to a different recipient.

## Insolvency

Settlement can establish a winning liability even when the book is temporarily unable to pay it. If the solvency breaker is active, claims may be blocked while the book attempts to restore coverage through buybacks or additional capital.

If the shortfall cannot be recovered, the protocol applies a proportional socialized-loss adjustment across affected positions. The adjustment is calculated before claims resume, so calling earlier does not avoid the reduction or consume funds owed proportionally to other users.

The position remains **Resolved — Won**, but the final claimable amount may be lower than the payout recorded at opening. See [LP Accounting & Buffer](technical-reference/lp-accounting-and-buffer.md#insolvency-and-socialized-loss) for the allocation model.

## Delayed Settlement

Network congestion, keeper downtime, safety restrictions, or Hyperliquid availability can delay settlement. A delay does not by itself change the position's boundaries or fixed payout. Stored mark readings allow an observed crossing to be settled after normal operation resumes.
