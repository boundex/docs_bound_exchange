# Setting Your Range

When you provide liquidity, you choose the price range over which your assets will be deployed. This is one of the most powerful - and important - decisions you make as an LP.

## Range options

### Full range (default)

Your assets are spread evenly across all prices. This is the simplest option - your position is always active regardless of price movement.

### Preset ranges

Toggle between **5%**, **10%**, or **20%** width centered around the current price. Tighter ranges earn more fees per unit of capital when price stays within range.

### Custom range

Drag the sliders or enter prices directly into the textbox to fully customize your range.

## How ranges work

* **Min price** → The point where all of your Bitcoin has been sold into the selected token. At this level, your position is 100% in the selected token.
* **Max price** → The point where all of your selected token has been sold back into Bitcoin. At this level, your position is 100% BTC.
* **Between min and max** → Your position is a mix of both assets - effectively dollar-cost averaging in and out of the selected token as price moves.

{% hint style="success" %}
Custom ranges are a powerful tool to execute a specific trading strategy - whether you want to buy/sell aggressively at certain prices, or more passively across a wide range.
{% endhint %}

{% hint style="info" %}
There is no fixed minimum deposit. The required token amounts depend on your chosen range. As long as at least one of the two token amounts is greater than zero, your position is valid. Tighter ranges may require only one token depending on where the current price sits relative to your min and max.
{% endhint %}
