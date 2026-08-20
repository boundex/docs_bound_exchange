# Closing Early

An active Boundary Perp may be closed before its terminal outcome by accepting a contract-quoted buyback.

## Requesting a Buyback

The contract values the position using current market conditions and returns an early-close quote. The value generally reflects:

* The current probability of the selected outcome
* The position's fixed payout
* Expected hedge adjustment costs
* The book's current exposure
* The applicable spread and protocol fee

## Accepting or Declining

If you accept a valid quote:

1. The contract validates the position and quote again.
2. The buyback amount is paid to the position owner.
3. The position becomes **Resolved — Closed Early**.
4. The position leaves the active book.
5. The protocol updates its hedge.

If you decline the quote or let it expire, the position remains active and its original boundaries and fixed payout are unchanged.

## Important Considerations

* A buyback value is not guaranteed to exceed the original stake.
* Early-close quotes expire as market conditions change.
* A terminal product outcome takes precedence if it is established before the buyback executes.
* Early closing may be temporarily unavailable when safety limits restrict outflows or quoting.
