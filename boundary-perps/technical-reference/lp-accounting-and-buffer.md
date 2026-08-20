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

The probability is capped to `[0, 1]`. If there is evidence that a boundary has already crossed but settlement has not yet executed, the position is marked at its resolved value:

* Full payout if the chosen boundary crossed first.
* Zero if the non-chosen boundary crossed first.

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

## Insolvency and Socialized Loss

Breaker 2 is the first response when book assets approach the book's marked liabilities. It stops new positions and permits only buybacks, which retire liabilities and can improve coverage.

If the book remains unable to cover its liabilities, claims are blocked while the contract calculates a last-resort socialized loss. The positive book shortfall is:

```text
book_shortfall = max(0, marked_liability - book_assets)
```

The shortfall is allocated across affected user positions in proportion to their marked position values:

```text
user_shortfall_share =
  user_position_value / total_affected_position_value
  * book_shortfall

adjusted_amount = max(0, normal_amount - user_shortfall_share)
```

`book_shortfall` is expressed as a positive amount. This avoids the sign ambiguity that would result from subtracting a negative LP value.

The contract snapshots the affected positions, their values, total affected position value, book assets, and marked liabilities for the allocation. The adjustment applies consistently to affected claims and buybacks so claim order cannot allow early callers to exhaust the remaining assets. The allocated reduction remains in the book and helps restore solvency. Once the contract has finalized the allocation and the applicable safety conditions are satisfied, users can receive their adjusted amounts.

Socialized loss is a last-resort mechanism. It does not change which boundary won, but it can reduce the amount ultimately paid below the payout recorded when the position opened.

## LP Share Issuance During Losses

Ordinary LP deposits use the pre-deposit NAV per share:

```text
NAV per share = NAV / total share supply
shares minted = deposit / NAV per share
```

If NAV remains positive but has declined, the share price is lower and a new depositor receives more shares for the same deposit. Existing LPs retain their proportional ownership of the impaired pool before the new deposit.

If NAV is zero or negative, the ordinary formula cannot produce a valid positive share amount. A deposit may recapitalize the book economically, but share issuance requires an explicit recapitalization rule rather than division by a non-positive NAV. The deployed contract's recapitalization policy is authoritative for that edge case.

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
