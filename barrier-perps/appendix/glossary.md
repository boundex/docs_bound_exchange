# Glossary

## Barrier

An upper or lower price level defining a Vanilla Barrier Perp. The upper barrier crosses when the mark reaches or exceeds it. The lower barrier crosses when the mark reaches or falls below it.

## Chosen Barrier

The barrier selected by the user. Crossing it first produces **Resolved — Won**.

## Non-Chosen Barrier

The other barrier. Crossing it first produces **Resolved — Lost**.

## Stake

The USDC committed to a position. It is the position's maximum loss and becomes part of the book's hedge collateral.

## Fixed Payout

The gross total claimable after a win, including the returned stake. It is fixed when the position opens.

## Protocol Fee

A fee charged on top of the stake at opening. It accrues separately from assets backing positions and LP shares.

## Buyback

A contract-quoted early close of an active position. Accepting it pays the quoted amount and produces **Resolved — Closed Early**.

## First Touch

The first qualifying stored or in-call mark reading at or beyond one of the two barriers.

## Book

An isolated Barrier Perps contract instance with its own assets, liabilities, positions, LP buffer, risk controls, and HyperCore hedge account.

## Open Interest

The sum of stakes across active positions in one book. It is capped by `maxOI`.

## Net Exposure

For one underlying, the signed sum of active positions' hedge deltas. This is the exposure the protocol hedges in that underlying's perpetual.

## Hedge Notional

The summed absolute notional of the protocol's per-underlying net hedge positions.

## Buffer

LP-funded USDC capital that supplements user stakes, supports hedge margin, and absorbs hedge and cost variance.

## Book Assets

The book contract's payout-backing USDC plus its HyperCore account equity. Accrued protocol fees are excluded.

## Marked Liability

The sum of active positions' current probability-weighted payouts plus resolved but unclaimed winning payouts at full value.

## NAV

The net asset value belonging to LPs:

```text
NAV = book_assets - marked_liability
```

## Core Equity

The book's HyperCore account equity, including posted collateral and unrealized hedge profit or loss.

## Spread

The pool margin included in a quote after expected funding and execution cost recovery. It combines a base spread and a signed book-imbalance adjustment.

## Quote

An indicative payout and fee for one product configuration. It expires and is recomputed during acceptance.

## `minPayout`

The minimum binding payout a user will accept. The opening transaction reverts if repricing produces a lower amount.

## Mark Worker

A permissionless service that calls the contract to read and store authenticated HyperCore mark prices.

## Settlement Keeper

A service that watches positions and submits permissionless settlement calls when valid crossing evidence exists.

## Restricted

A circuit-breaker mode that permits only actions considered safe or corrective for the active condition.

## Freeze

A circuit-breaker mode that halts most protocol actions and outflows while still allowing LP deposits and mark-worker publications.
