# FAQ



<details>

<summary>Can I close my position before either price level is reached?</summary>

Yes.

You may choose to **cash out** your Binary Perp at any time before it resolves.

A cash-out closes the underlying perpetual position at the current market price and settles your position based on the realized profit or loss. Standard trading fees and the Builder Fee apply.

</details>

<details>

<summary>Can I modify my Binary Perp after it has been created?</summary>

No.

Once a Binary Perp has been created, its parameters cannot be changed. This includes:

* Price levels
* Prediction direction
* Position size

If you no longer wish to keep the position open, you may cash out or wait for it to resolve naturally.

</details>

<details>

<summary>What happens if I modify the position directly on Hyperliquid?</summary>

Binary Perps assume that the underlying perpetual position remains unchanged after it has been created.

If you manually modify the position on Hyperliquid, for example by closing the position, cancelling orders, adjusting margin, or changing leverage, Bound can no longer guarantee that the Binary Perp behaves as intended.

When this occurs, the Binary Perp is marked as **Voided**, while the underlying perpetual position remains entirely under your control.

</details>

<details>

<summary>Why can I only have one active Binary Perp per asset?</summary>

Each Binary Perp corresponds to a single perpetual position on Hyperliquid.

Allowing multiple Binary Perps on the same asset would make it difficult to reliably track liquidation levels, take-profit orders, and the overall position lifecycle.

Once your existing Binary Perp has been resolved or voided, you may create another on the same asset.

</details>

<details>

<summary>Why doesn't my execution price exactly match my selected price?</summary>

Binary Perps use market orders for both entry and exit.

During periods of high volatility or limited liquidity, your actual execution price may differ slightly from the current market price due to market slippage.

</details>

<details>

<summary>Does Bound ever take custody of my funds?</summary>

No.

Your assets always remain in your own Hyperliquid account.

Every transaction requires your wallet signature, and Bound cannot move funds or execute trades on your behalf.

</details>

<details>

<summary>Does a Binary Perp guarantee my estimated payout?</summary>

No.

The estimated payout shown before opening a position is based on the selected price levels and current market conditions.

Your actual return may differ due to:

* Trading fees
* Builder Fee
* Funding payments
* Market execution price

</details>

<details>

<summary>Which assets are supported?</summary>

Supported assets depend on the markets currently available on Hyperliquid.

The list of supported assets may expand over time as additional markets become available.

</details>

<details>

<summary>Can Bound liquidate or close my position?</summary>

No.

Bound prepares transactions, monitors position state, and records the outcome of your Binary Perp.

Every trading action, including opening, closing, and cashing out a position, requires your explicit wallet signature. Bound cannot independently open, close, or modify positions on your behalf.

</details>
