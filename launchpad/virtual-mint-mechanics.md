# Virtual Mint Mechanics

## How the virtual mempool works

Bound operates a single, global virtual mempool for all mints on the platform. Every time a Bitcoin block is mined on mainnet, Bound mines a corresponding virtual block.

When you submit a mint request, the BTC you spend is deducted from your Trading Wallet balance - just like a swap. Your request enters the virtual mempool and competes for inclusion in the next virtual block.

## Block inclusion rules

| Parameter                   | Value                                        |
| --------------------------- | -------------------------------------------- |
| Max mints per virtual block | 4,000                                        |
| Selection criteria          | Highest bids first; ties broken by timestamp |
| Tokens per mint             | 10,000                                       |
| Min price per token         | 0.01 sats                                    |
| Max mints per transaction   | 1,000                                        |

Only requests broadcast **before the timestamp of the corresponding Bitcoin block** are eligible.

## Minimum liquidity requirement

For a token to launch, it must reach a minimum of **0.08 BTC** committed across all mints.

* If `minLiq` is not met when minting ends: all BTC is refunded to participants, and the token does not launch
* If `minLiq` is met: the token launches and trading begins

## What happens on launch

{% stepper %}
{% step %}
### The committed BTC is paired with **20% of the token supply** and supplied as a full-range liquidity position on the Runes AMM at the **1.00% fee tier**
{% endstep %}

{% step %}
### Remaining tokens (up to 800M) are distributed to mint participants
{% endstep %}

{% step %}
### If fewer than 800M tokens were minted, the remaining unminted tokens are distributed **pro-rata** to participants
{% endstep %}

{% step %}
### Diamond Hands tracking begins
{% endstep %}
{% endstepper %}

## Refunds

If your mint request was not included in a block, or the token did not reach `minLiq`, you can claim a refund:

```
Refund amount = BTC spent − BTC confirmed in blocks
```

Go to the **Portfolio** tab to claim any available refunds.
