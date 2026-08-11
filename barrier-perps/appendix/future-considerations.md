# Future Considerations

The following items are possible extensions, not commitments or active protocol features.

## Additional Barrier Perps Products

Barrier Perps are structured as a product family. Future products can define different barrier events, payoff structures, or pricing models while reusing shared quoting, lifecycle, settlement, liquidity, and safety infrastructure.

Each product should have:

* A clear outcome definition
* Product-specific validation rules
* A dedicated pricing model
* Explicit settlement evidence and precedence
* Defined early-close behavior

## Multiple Competing Pools

Future versions may allow multiple independent pools to quote the same product. Users could compare payouts or route through an aggregator.

Introducing pool competition requires rules for approved contracts, routing, migrations, liquidity fragmentation, and consistent settlement behavior.

## Permissionless LP Access

LP participation may begin with an allowlist. A future version could allow any eligible wallet to deposit into a book.

Permissionless access would require finalized policies for:

* Deposits and withdrawal queues
* Share transferability
* Disclosure of NAV and liabilities
* Emergency restrictions
* Jurisdictional or compliance requirements

## Secondary Market

A future secondary market could allow an active position to be transferred or sold before settlement.

This differs from the current buyback model because the position would remain active under a new owner rather than leaving the book. The design would need to preserve ownership, claim routing, lifecycle state, and liability accounting.

## Dynamic Imbalance Pricing

The launch pricing model may use a constant imbalance coefficient `lambda`. Future versions could adjust it according to:

* Current funding rates
* Buffer utilization
* Hedge liquidity
* Book concentration
* Realized execution costs
* Market volatility

Dynamic parameters would need onchain bounds and transparent update rules so quotes remain predictable and accepted positions remain unchanged.

## More Advanced Probability Models

The initial model uses a linear first-touch probability approximation. Future pricing could incorporate volatility surfaces, directional drift, jumps, market closures, and asset-specific path behavior.

Any new model must remain deterministic enough for contract execution and must preserve the user's minimum-payout protection during quote acceptance.
