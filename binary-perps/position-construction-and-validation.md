# Position Construction & Validation

Before a Binary Perp can be created, Bound validates the requested parameters and constructs a perpetual position that reproduces the desired binary outcome.

If any validation fails, the position is rejected and no transactions are prepared.

***

### Validation

Every Binary Perp must satisfy the following requirements before construction begins.

#### Supported Price Range

Both the upper and lower price levels must fall within the maximum supported distance from the current market price.

***

#### Minimum Price Distance

The selected liquidation price must remain achievable within the supported leverage limits for the underlying asset.

If the requested price levels would require leverage beyond the supported maximum, the position cannot be constructed.

***

#### Position Size

The position size must fall within the configured minimum and maximum limits.

***

#### Available Balance

The Hyperliquid account must contain sufficient USDC to fully collateralize the position.

***

#### Existing Positions

Only one active Binary Perp is permitted per asset.

If another Binary Perp already exists for the selected asset, a new position cannot be created until the existing one is resolved.

***

#### Builder Fee Approval

The user must have completed the one-time builder fee approval before creating their first Binary Perp.

***

### Position Construction

After validation succeeds, Bound constructs an isolated perpetual position on Hyperliquid.

The construction process determines:

* Position direction (Long or Short)
* Position margin
* Required leverage
* Take-profit level
* Liquidation level

These values are calculated automatically based on the user's selected price levels and position size.

***

### Isolated Margin

Binary Perps always use **isolated margin**.

The position margin is equal to the user's selected position size, ensuring that the maximum possible loss is limited to that amount.

Cross margin is not supported.

***

### Position Direction

The selected prediction determines the position direction.

| Prediction                | Position |
| ------------------------- | -------- |
| Upper price reached first | Long     |
| Lower price reached first | Short    |

***

### Take-Profit & Liquidation

The selected price level becomes the position's take-profit.

The opposite price level determines the liquidation price.

Bound automatically calculates the leverage required to align the perpetual position with these two price levels.

***

### User-Favorable Rounding

Exact liquidation prices cannot always be achieved due to leverage increments, tick sizes, and exchange mechanics.

When rounding is required, Bound always rounds in favor of the user.

This guarantees that liquidation will never occur before the selected liquidation price is reached, although liquidation may occur slightly beyond it.

***

### Entry Slippage

Market orders are submitted using a configurable maximum slippage.

After execution, Bound records the actual entry price, liquidation price, and payout values based on the completed trade.

***

### Construction Complete

Once the position has been successfully constructed and executed, the Binary Perp enters the **Active** state and begins normal lifecycle monitoring.
