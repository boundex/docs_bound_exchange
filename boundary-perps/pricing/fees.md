# Fees

Boundaries charge two kinds of fees: an **upfront fee** on the size of your position, and a **spread** that is a share of your profit. If your position loses money, you pay no spread.

## Upfront fee

Charged once, when you open a position:

* **Protocol fee:** a percentage of your stake.
* **Hedge opening cost:** the Hyperliquid trading cost of opening the hedge behind your position.

Both are shown before you confirm, and are taken from the amount you send.

## Spread

The spread is a share of your profit. It covers the liquidity pool's margin, the book's balance and expected funding.

* **Win at your boundary:** you receive your stake plus your profit, minus the spread on that profit.
* **Close early with a gain:** the spread is taken from the gain you have so far.
* **Close early at a loss, or lose:** no spread is charged.

## Closing early

When you close early, two more costs are deducted from your buyback:

* **Hedge closing cost:** the Hyperliquid cost of unwinding your hedge, calculated at that moment from the hedge's actual size.
* **Early-close protocol fee:** a percentage of the buyback, when enabled.

The Current Value shown in the app already includes these costs, so the amount you see is the amount you receive. See [Closing Early](../closing-early.md).

## Example

Illustrative only: a $100 stake on a 2x position, with a spread of 20% of profit (10% of the 2x payout). Upfront fees and the hedge closing cost are not shown.

| Scenario | Hedge value | You receive |
| --- | --- | --- |
| Win at your boundary | $200 | $180 |
| Halfway to your boundary | $150 | $140 |
| Halfway to the losing boundary | $50 | $50 |
| Lose | $0 | $0 |
