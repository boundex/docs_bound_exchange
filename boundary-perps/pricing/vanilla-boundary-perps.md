# Vanilla Boundary Perps Pricing

Vanilla Boundary Perps pricing begins with the probability that the chosen boundary is reached before the other boundary. The position's fair payout is then adjusted for expected hedge costs and the pool's spread.

## Boundary Geometry

Let:

* `S` be the current Hyperliquid mark price.
* `U` be the upper boundary.
* `L` be the lower boundary.

For an upper-first position, the simplified touch probability is:

```
upper-first probability = (S − L) / (U − L)
```

For a lower-first position:

```
lower-first probability = (U − S) / (U − L)
```

These formulas illustrate how boundary placement influences a quote. The binding quote is always the value returned by the contract.

## How Boundary Placement Affects Payout

All else equal:

* Moving the chosen boundary closer makes the selected outcome more likely and generally lowers the payout multiple.
* Moving the chosen boundary farther away makes the selected outcome less likely and generally raises the payout multiple.
* Moving the non-chosen boundary changes both the probability and the hedge required to support the position. It has opposite behavior to moving chosen boundaries.

## Quoted Payout

At a high level, the quote applies the chosen outcome's probability to the portion of the stake remaining after expected costs and spread:

```
fixed payout =
  (stake − funding reserve − execution reserve − spread)
  / chosen-outcome probability
```

## Worked Example

Assume:

* BTC mark price: **$100,000**
* Upper boundary: **$110,000**
* Lower boundary: **$97,500**
* Chosen outcome: **Upper first**
* Stake: **1,000 USDC**

The simplified upper-first probability is:

```
(100,000 − 97,500) / (110,000 − 97,500)
= 2,500 / 12,500
= 20%
```

Before costs and spread, a 20% outcome implies a fair gross payout of approximately:

```
1,000 / 20% = 5,000 USDC
```

The actual quote may be lower or higher depending on expected costs and how the position changes the book's existing exposure. The accepted onchain quote, not this simplified estimate, determines the fixed payout.
