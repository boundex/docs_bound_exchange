# Limitations & Design Decisions

Binary Perps are designed to provide a simple binary trading experience while maintaining user custody and leveraging Hyperliquid's perpetual infrastructure.

The following design decisions are fundamental to how Binary Perps operate.

***

### Isolated Margin Only

Binary Perps always use **isolated margin**.

The position margin is equal to the amount committed by the user, ensuring that the maximum possible loss is limited to the selected position size.

Cross margin is not supported, as it could expose additional account funds beyond the intended risk.

***

### One Active Position Per Asset

Only one active Binary Perp may exist per asset within a single account.

This prevents multiple overlapping positions from interfering with each other's take-profit, liquidation level, and lifecycle tracking.

A new Binary Perp may be created once the existing position has been resolved or voided.

***

### User-Controlled Accounts

Bound never has custody of user assets and never receives delegated trading authority.

Every action, including:

* Opening a position
* Closing a position
* Cashing out

requires an explicit wallet signature from the user.

***

### External Modifications

Binary Perps assume the underlying perpetual position remains unchanged after creation.

If the position is modified directly on Hyperliquid, for example by changing leverage, adjusting margin, cancelling orders, or manually closing the position, Bound can no longer guarantee that the Binary Perp behaves as intended.

Such positions are marked as **Voided**.

***

### Market Execution

Binary Perps use market execution for both position entry and take-profit execution.

This prioritizes execution certainty over execution price.

As a result, the actual execution price may differ slightly from the selected price level due to market conditions and available liquidity.

***

### User-Favorable Liquidation Rounding

Exact liquidation prices cannot always be achieved due to exchange mechanics such as leverage increments, maintenance margin calculations, and price tick sizes.

When adjustments are required, Bound always rounds in favor of the user.

This guarantees that liquidation will never occur before the selected liquidation price is reached, although it may occur slightly beyond it.

***

### Funding Rate Drift

Funding payments continue to accrue while a Binary Perp remains open.

Over time, funding may slightly affect the effective liquidation price of the underlying perpetual position.

This is an inherent property of perpetual futures and is not adjusted by Bound.
