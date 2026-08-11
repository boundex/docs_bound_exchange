# Vanilla Barrier Perps Pricing

Vanilla Barrier Perps pricing begins with the probability that the chosen barrier is reached before the other barrier. The position's fair payout is then adjusted for expected hedge costs and the pool's spread.

## Barrier Geometry

Let:

* `S` be the current Hyperliquid mark price.
* `U` be the upper barrier.
* `L` be the lower barrier.

For an upper-first position, the simplified touch probability is:

```text
upper-first probability = (S − L) / (U − L)
```

For a lower-first position:

```text
lower-first probability = (U − S) / (U − L)
```

These formulas illustrate how barrier placement influences a quote. The binding quote is always the value returned by the contract.

## How Barrier Placement Affects Payout

All else equal:

* Moving the chosen barrier closer makes the selected outcome more likely and generally lowers the payout multiple.
* Moving the chosen barrier farther away makes the selected outcome less likely and generally raises the payout multiple.
* Moving the non-chosen barrier changes both the probability and the hedge required to support the position.

## Quoted Payout

At a high level, the quote applies the chosen outcome's probability to the portion of the stake remaining after expected costs and spread:

```text
fixed payout =
  (stake − funding reserve − execution reserve − spread)
  / chosen-outcome probability
```

The protocol fee is charged separately and is not deducted from the stake in this formula.

## Worked Example

Assume:

* BTC mark price: **$100,000**
* Upper barrier: **$110,000**
* Lower barrier: **$97,500**
* Chosen outcome: **Upper first**
* Stake: **1,000 USDC**

The simplified upper-first probability is:

```text
(100,000 − 97,500) / (110,000 − 97,500)
= 2,500 / 12,500
= 20%
```

Before costs and spread, a 20% outcome implies a fair gross payout of approximately:

```text
1,000 / 20% = 5,000 USDC
```

The actual quote may be lower or higher depending on expected costs and how the position changes the book's existing exposure. The accepted onchain quote, not this simplified estimate, determines the fixed payout.
