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

### Redeem

To redeem bUSD back to a stablecoin on EVM:

1. Send bUSD from your Bitcoin Bound wallet to Bound's hot wallet
2. The Bitcoin transaction embeds your EVM address and the target stablecoin ID in an OP_15 script
3. Bound reads the script and sends the corresponding stablecoin to your EVM address

### Backing assets

bUSD is backed 1:1 by **USDC and USDT**, held in Ethereum multisig wallets:

| Wallet | Address |
| --- | --- |
| ETH available reserves | `0x1c51243C0aCFA2fBB2E06bfc2b9066C96Ff6d604` |
| ETH offline reserves | `0x00DA6e7A5Cd24bC1a79D0175fFB5724f8524F62f` |

Amounts exceeding $100,000 in the available reserves wallet are automatically swept to offline reserves.

## Proof of reserves

bUSD circulating supply and backing can be verified on-chain at any time.

**Circulating supply**

```
Circulating bUSD = Total Supply − BTC available reserves − BTC offline reserves
```

| Wallet | Address |
| --- | --- |
| BTC available reserves | `bc1pkg6r6s0yevd465utaumyj5qf3dwgchzx83w4hlje776hk4qc9zus8dvn38` |
| BTC offline reserves | `bc1phr6t5mw4d58mqu9nd0gp9c9hfxe60wksdg5dxn330l75ucnpc56qatke8a` |

**Backing USD**

```
Backing USD = ETH available reserves + ETH offline reserves
```

**Tracking redeem requests**

Each redeem transaction embeds the user's EVM address on-chain in the Bitcoin script. You can verify any request by matching the Bitcoin transaction from the Bound wallet address with the EVM address in the redeem script.

