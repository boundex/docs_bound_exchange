# bUSD Technical Spec

## What is bUSD

bUSD is Bound's native stablecoin on Bitcoin - a **Runes-based token backed 1:1 by other stablecoins**. It serves as the settlement layer for all Bound structured products: loans, options, and escrow all settle in bUSD.

## Role in the stack

* **Loans** - borrowed funds are denominated in bUSD
* **Options** - collateral and settlement use bUSD
* **Escrow** - all structured product settlement goes through bUSD
* **Integration** - other Bitcoin-native protocols can integrate bUSD as a stable settlement layer

## Mint & redeem

bUSD is live with mint and redeem functionality.

{% hint style="info" %}
**VERIFY** - Technical mint/redeem mechanics, supported backing stablecoins, fees, limits, and proof of reserves approach to be confirmed with engineering team before publishing.
{% endhint %}
