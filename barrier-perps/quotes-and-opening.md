# Quotes & Opening a Position

A Barrier Perp opens through a quote-and-accept flow. No position exists until the acceptance transaction succeeds.

## Requesting a Quote

Provide the inputs required by the selected product. For Vanilla Barrier Perps, these are:

* Underlying asset
* Upper barrier
* Lower barrier
* Chosen barrier
* USDC stake

The contract checks the request before returning a quote.

## Validation

A request may be rejected if:

* The current mark is not strictly between the barriers.
* A barrier is outside the supported distance from the mark.
* The non-chosen barrier is closer than the supported minimum.
* The stake is outside the supported range.
* The wallet does not have enough USDC for the stake and fee.
* The resulting payout does not satisfy protocol requirements.
* The active book does not have enough capacity.
* A safety restriction or pause prevents new positions.

Validation rules can vary by asset because leverage, volatility, liquidity, and gap risk differ across markets.

## Reviewing the Quote

Before accepting, review:

| Field | Meaning |
| --- | --- |
| Stake | Maximum amount at risk |
| Fixed payout | Total claimable amount if the position wins, including the stake |
| Protocol fee | Fee paid in addition to the stake |
| Barriers and outcome | Conditions that determine the result |
| Quote expiry | Deadline for accepting the quote |

## Accepting the Quote

Acceptance happens atomically. The contract:

1. Revalidates the position against current state.
2. Recomputes the payout using the current mark and book exposure.
3. Checks the recomputed payout against your minimum payout.
4. Transfers the stake and fee.
5. Locks the barriers and payout.
6. Opens the position and adds it to the book.

If any check fails, the transaction reverts and no position opens.

## After Opening

The position becomes **Active**. Its maximum loss, barriers, and payout are fixed. Bound then updates the protocol's hedge to account for the change in net exposure.
