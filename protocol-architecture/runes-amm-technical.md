# Runes AMM Technical

## Pricing model

Bound's AMM uses **Concentrated Liquidity** - a variation on the constant product formula (x\*y=k) where LPs set a custom price range for their assets.

Concentrated Liquidity calculations are implemented in an **EVM environment**. When a user requests a quote, the AMM queries this EVM environment to determine the correct output amount.

## OP\_12 encoding

Every Bound AMM transaction encodes structured data using `OP_12` - a Bitcoin Script opcode repurposed to carry AMM data in dust-value outputs.

Each `OP_12` output encodes:

* **Transaction type** - provide liquidity, swap, withdraw liquidity, collect fees, etc.
* **Input and output asset amounts**
* **Token identifiers**
* **Trading fee information**
* **Price range** (upper and lower tick, if applicable)

This approach enables fully on-chain AMM execution while remaining lightweight and efficient - no smart contract layer on Bitcoin required.

## Transaction types

Bound supports 8 AMM transaction types:

| Type                   | Description                                 |
| ---------------------- | ------------------------------------------- |
| Init Pool              | Create a new pool and add initial liquidity |
| Add Liquidity          | Add a new position to an existing pool      |
| Swap                   | Exchange one asset for another              |
| Withdraw Liquidity     | Remove a position from a pool               |
| Collect Fees           | Claim accrued trading fees                  |
| Increase Liquidity     | Add to an existing position                 |
| Migrate Pool           | Move pool to a new address                  |
| Migrate Pool Init UTXO | Move pool init UTXO to a new address        |

## FROST Multisig

Bound validators use **FROST (Flexible Round-Optimized Schnorr Threshold Signatures)** for decentralized signing of Bitcoin transactions.

Key properties:

* **3-of-5 threshold** - 3 signers required out of 5
* **Distributed key generation** - no single party ever holds the full private key
* **Key management** - signer keys stored in AWS Secrets Manager
* **Resilience** - supports key refresh on leak, recovery on loss, signer replacement
