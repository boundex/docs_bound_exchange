# Liquidity & Hedging

Bound manages Barrier Perps as a portfolio rather than opening a separate perpetual futures position for every user position.

## Per-Underlying Net Books

For each underlying asset, the protocol calculates the combined exposure of all active positions. Opposing positions can offset one another, reducing the perpetual exposure required to hedge the book.

Exposure does not net across different assets. BTC exposure is hedged with BTC perpetuals, ETH exposure with ETH perpetuals, and so on.

## Dynamic Hedging

The contract maintains a target hedge based on the book's current net exposure. It updates that target when:

* A new position opens.
* A position settles.
* A position closes early.
* Market movement changes the book's exposure.

The protocol executes the hedge on Hyperliquid. Hedge gains and losses are part of the book's assets and help fund position outcomes.

## Stakes and Liquidity Buffer

User stakes provide the primary collateral supporting the hedge. Liquidity providers supply an additional USDC buffer that:

* Tops up hedge margin beyond user stakes
* Absorbs funding and execution variance
* Supports payout obligations during market movement
* Earns the spread included in position pricing

LP capital is junior to winner payouts: losses or hedge variance reduce liquidity-provider value before changing a position's fixed payout under normal operation.

## Book Isolation and Rotation

Each book is an isolated contract instance with its own assets, buffer, positions, safeguards, and open-interest cap. When a book reaches its capacity, it stops accepting new positions while existing positions continue to settle or close.

Additional capacity can be provided through a new isolated book. This limits how much exposure a single contract fault or hedge failure can affect.

## Why Netting Matters

If one group of positions benefits from upward price movement and another benefits from downward movement, part of their exposure offsets internally. Hedging only the remainder can reduce:

* Required perpetual notional
* Required hedge collateral
* Funding costs
* Trading activity

These efficiencies can be reflected in the quotes offered to users.
