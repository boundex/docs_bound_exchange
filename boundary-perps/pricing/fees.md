# Fees

Boundaries charge a small fee when you open and when you close early. The pool's margin is a **share of your profit**, taken only if you make money. If your position loses, or you close it at a loss, you pay no profit share.

| Fee | When | Rate today |
| --- | --- | --- |
| Open fee | Every position, at open | 1% of stake |
| Hedge execution | Every position, priced into the payout at open | Depends on hedge size |
| Profit share | Only on profit: win at target, or close early in profit | Capped at 50% of profit |
| Early-close fee | When you close early | 1% of the amount you receive |

Rates are protocol settings and can change. A change applies only to new positions: an open position keeps the payout, profit share and early-close fee it had when it opened.

## Opening a position

**Open fee.** 1% of your stake goes to the protocol. The rest is your priced stake.

**Hedge execution.** Every position is hedged on Hyperliquid. The trading cost of opening *and* closing that hedge (taker fee plus slippage) is prepaid at open by setting the payout slightly lower. Larger hedges cost more, because slippage grows with size. You are never charged the hedge close cost again later.

**Your payout is fixed at open.** The quote shows what you receive if the price hits your target first: the payout, minus the profit share. That number does not change for the life of the position.

## Profit share

The pool earns a share of your profit instead of charging a spread up front. It covers the pool's margin, the balance of the book and expected funding costs on the hedge.

* **Win at target:** you receive the payout minus the profit share shown in your quote.
* **Close early in profit:** the share is scaled to the profit you have so far. Half of your target profit means half of the share.
* **Lose, or close early at a loss:** no profit share is charged.

The share can never be more than 50% of your profit, and a quote is only offered if a win leaves you with more than your stake.

## Closing early

Your position's value at any moment is its payout times the current chance of hitting your target first. When you close early:

1. If you are in profit, the matching part of the profit share is deducted.
2. The 1% early-close fee is deducted from what is left.

The Current Value shown in the app is the amount you receive after both.

## Example

$20 on BTC Up, stop 5% below, target 5% above (a fair 2× bet). Figures use today's rates.

| Step | Amount |
| --- | --- |
| Stake after 1% open fee | $19.80 |
| Payout after hedge execution | $38.74 |
| Profit share at target | $3.58 |
| **You receive if BTC hits the target** | **$35.16** |

Closing early, same position:

| Price | Value | Profit share | You receive (after 1% fee) |
| --- | --- | --- | --- |
| Unchanged since open | $19.37 | $0 | $19.18 |
| Halfway to target | $29.05 | $1.67 | $27.11 |
| Halfway to stop | $9.69 | $0 | $9.59 |
| Stop hit first | $0 | $0 | $0 |
