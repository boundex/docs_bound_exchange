# Monitoring & Resolution

Once a Binary Perp becomes **Active**, Bound continuously monitors the underlying perpetual position on Hyperliquid to ensure its status remains synchronized with the application.

Whenever a significant event occurs, Bound records the outcome and updates the position accordingly.

***

### Monitored Events

Bound monitors the following events throughout the lifetime of a Binary Perp:

* Take-profit execution
* Liquidation
* User cash-out
* Position modifications
* Margin changes
* Order cancellations

These events determine when a Binary Perp transitions into its final state.

***

### Position Resolution

A Binary Perp is considered resolved when one of the following occurs.

#### Take-Profit Execution

If the selected price level is reached first, Hyperliquid executes the take-profit order and closes the position.

The position is recorded as:

**Resolved – Won**

***

#### Liquidation

If the opposite price level is reached first, the perpetual position is liquidated.

The position is recorded as:

**Resolved – Lost**

***

#### Cash-Out

Users may choose to close their position before either price level is reached.

When a cash-out is requested:

1. Bound prepares a transaction bundle.
2. The take-profit order is cancelled.
3. A reduce-only market order is prepared.
4. The user signs the transaction.
5. Hyperliquid executes the close.

Once the close is verified, the position is recorded as:

**Resolved – Cashed Out**

***

### External Modifications

Binary Perps must remain under the structure originally created by Bound.

If the user modifies the underlying perpetual position directly on Hyperliquid, Bound can no longer guarantee that the Binary Perp behaves as intended.

Examples include:

* Closing the position manually
* Cancelling the take-profit order
* Adding or removing margin
* Changing leverage

When this occurs, the Binary Perp is marked as:

**Voided**

The perpetual position remains entirely under the user's control.

***

### Resolution Records

For every completed Binary Perp, Bound records the final execution details, including:

* Entry price
* Exit price
* Fees
* Funding payments
* Final proceeds
* Resolution status

These records allow the complete outcome of a Binary Perp to be reconstructed without additional queries to Hyperliquid.

***

### Edge Cases

#### Price Gaps

Take-profit orders execute as market orders.

If the market gaps beyond the selected price level, the position closes at the best available execution price.

***

#### Funding Payments

Funding payments continue to accrue while a position remains open.

As a result, the effective liquidation price may drift slightly over time.

***

#### Venue Downtime

If Hyperliquid is unavailable:

* New Binary Perps cannot be created.
* Existing positions remain governed by Hyperliquid's matching engine.
* Bound synchronizes position status once connectivity is restored.

***

#### Cash-Out Race Conditions

If a take-profit execution or liquidation occurs before a pending cash-out is processed, the venue event takes precedence.

The Binary Perp resolves according to the actual outcome on Hyperliquid.
