# LP Accounting & Buffer

Liquidity providers fund the additional capital buffer behind each isolated book. At launch, LP access may be restricted to approved participants.

## Purpose of the Buffer

User stakes provide the primary collateral for the protocol's hedge. The buffer provides capital beyond those stakes to:

* Support hedge margin when the replicated position requires more leverage than the stake can safely provide.
* Absorb hedge profit and loss.
* Absorb differences between expected and realized funding or execution costs.
* Support payouts during temporary hedge variance.
* Cover composition jumps as positions resolve or close.

Liquidity providers earn the spread through changes in the book's NAV.

## Book Assets

```text
book_assets =
  contract-owned USDC balances
  + HyperCore account equity
```

HyperCore account equity includes posted collateral and unrealized hedge profit or loss. Accrued protocol fees are excluded because they belong to Bound rather than the liquidity pool.

## Marked Liability

An active position is valued using its current chosen-outcome probability:

```text
active position value = current probability * fixed payout
```

The probability is capped to `[0, 1]`. If there is evidence that a barrier has already crossed but settlement has not yet executed, the position is marked at its resolved value:

* Full payout if the chosen barrier crossed first.
* Zero if the non-chosen barrier crossed first.

Resolved but unclaimed winning payouts remain liabilities at their full value.

```text
marked_liability =
  sum of active position values
  + resolved but unclaimed payouts
```

## Net Asset Value

```text
NAV = book_assets - marked_liability
```

LP ownership is represented by shares of this NAV. A deposit mints shares at the current NAV per share, and a withdrawal burns shares at the current NAV per share.

The implementation may use immediate withdrawals or a queued process, provided every exit prices at NAV per share and respects the book's safety constraints.

## Deposits and Withdrawals

LP deposits are accepted during normal, Restricted, and Freeze modes because they add capital to the book.

Withdrawals must not remove capital required to support active exposure. They are limited by:

* The configured minimum buffer.
* The shared outflow allowance.
* Active circuit breakers.

LP withdrawals are blocked while any breaker is tripped.

## Payout Priority

LP capital is junior to winner payouts. Hedge losses or cost variance reduce NAV before they affect the fixed liabilities owed to winning users under normal operation.

The source specification leaves catastrophic shortfall handling as a policy decision. Possible approaches include proportional reductions, a claim queue, or a migration freeze. Until that policy is finalized, the documentation should not imply that the buffer eliminates all solvency risk.

## Fee Accounting

The protocol fee is charged on top of the stake:

```text
amount transferred at open = stake + protocol fee
protocol fee = fee_rate * stake
```

The full stake enters payout and hedge calculations. Fees accrue to a Bound-owned balance inside the contract and remain outside `book_assets`, `marked_liability`, and LP NAV.

The `collectFees` function transfers accrued fees to a fee-handler contract. Fee collection consumes the shared outflow allowance and is halted during Freeze.

## Capacity Gates

Each book has a maximum open-interest limit measured as the sum of active user stakes. It also applies leverage and solvency limits.

As those limits are approached, the contract progressively restricts new risk:

* At the leverage ceiling, only exposure-reducing opens and buybacks may be accepted.
* At the solvency floor, only buybacks may be accepted.
* At the book's open-interest cap, new opens stop while settlement, claims, and buybacks continue.
