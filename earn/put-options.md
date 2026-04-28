# Put Options

{% hint style="warning" %}
Coming after initial launch. Details will be updated when this feature ships.
{% endhint %}

## How it works

A cash-secured put involves two parties:

**Provider (earn premium)**

* Lock bUSD as collateral
* Collect a premium upfront
* Agree to buy BTC at the strike price if the buyer exercises

**Buyer (hedge downside)**

* Pay a premium
* Receive the right to sell BTC at a guaranteed floor price before expiry
* Exercise is **unilateral via pre-signed transactions** - zero counterparty trust required

## Who it's for

**Providers:**

* bUSD holders seeking premium income

**Buyers:**

* Bitcoin miners hedging revenue
* Funds needing defined-risk downside protection
* Anyone wanting a fixed-cost hedge with a known maximum loss

## Key properties

* **Zero trust** - exercise requires no counterparty action; pre-signed transactions handle it automatically
* **Fixed-cost hedge** - buyers know their maximum loss upfront (the premium paid)
* **Defined risk** - no surprise outcomes for either side
