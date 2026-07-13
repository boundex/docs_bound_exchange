# Overview

### What are Binary Perps?

Binary Perps are a new way to express directional views on the market without predicting an exact price.

Instead of predicting **where** an asset will trade, users predict **which of two price levels will be reached first**.

For example, if Bitcoin is trading at **$100,000**, a user could create a Binary Perp with:

* **Upper Price:** $110,000
* **Lower Price:** $95,000

The user then predicts which price level will be reached first.

If their prediction is correct, the position settles in profit. If the opposite price level is reached first, the position is liquidated and the position is lost.

***

### How It Works

Binary Perps are powered by perpetual futures on [Hyperliquid](https://app.hyperliquid.xyz/).

When a Binary Perp is created, Bound constructs an isolated perpetual position within the user's own Hyperliquid account. The selected price level becomes the position's take-profit, while the opposite price level determines the liquidation price.

The distance between the two price levels determines the position's leverage and potential payout.

Throughout the lifecycle of a Binary Perp:

* Assets always remain in the user's own Hyperliquid account.
* Every transaction must be signed by the user's wallet.
* Bound never has custody of user funds or trading authority.

***

### Example

Assume BTC is trading at **$100,000**.

A user creates the following Binary Perp:

| Parameter   | Value             |
| ----------- | ----------------- |
| Upper Price | $110,000          |
| Lower Price | $95,000           |
| Prediction  | Upper Price First |
| Position    | 1,000 USDC        |

Bound constructs an isolated long perpetual position on Hyperliquid.

If BTC reaches **$110,000** before **$95,000**, the position closes in profit.

If BTC reaches **$95,000** first, the position is liquidated and the position is lost.

***

### Key Characteristics

* Predict which of two price levels will be reached first.
* Built on Hyperliquid perpetual futures.
* Positions remain entirely within the user's own Hyperliquid account.
* Every action requires the user's wallet signature.
* Bound never has custody of user funds.
* Positions may be closed early through a user-initiated cash-out before either price level is reached.

***

### Architecture

<figure><img src="../.gitbook/assets/seq.png" alt=""><figcaption></figcaption></figure>
