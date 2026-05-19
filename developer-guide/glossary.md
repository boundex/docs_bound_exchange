# Glossary

## Key terms

* **AMM (Automated Market Maker)**\
  A protocol that automatically prices assets based on a mathematical formula, allowing users to trade without a traditional order book.
* **Bound Auth**\
  Bound's passkey-based authentication system that creates self-custodial wallets across BTC, EVM, and Solana, with account backup and recovery support.
* **bUSD**\
  Bound's native Runes-based stablecoin on Bitcoin, backed 1:1 by other stablecoins. Settlement layer for all Bound structured products.
* **BIP322**\
  A Bitcoin standard for signing arbitrary messages with a Bitcoin wallet. Used by Bound for API authentication.
* **Concentrated Liquidity**\
  An AMM model where LPs provide liquidity within a specific price range, allowing for more capital-efficient liquidity provision.
* **Diamond Hands**\
  Bound Launchpad's game theory mechanic. Long-term holders of a launched token earn a growing share of LP trading fees as other holders sell.
* **FROST Multisig**\
  Flexible Round-Optimized Schnorr Threshold Signatures. Bound uses a 3-of-5 FROST setup for decentralized Bitcoin transaction signing.
* **LP (Liquidity Provider)**\
  A user who deposits assets into an AMM pool to enable trading and earn a share of trading fees.
* **OP\_12**\
  A Bitcoin Script opcode repurposed by Bound to encode structured AMM data in dust-value outputs, enabling on-chain DeFi without smart contracts on Bitcoin.
* **PSBT (Partially Signed Bitcoin Transaction)**\
  A Bitcoin transaction format that allows multiple parties to add their signatures before a transaction is broadcast. Bound uses PSBTs for all trading operations.
* **Runes**\
  A Bitcoin-native fungible token protocol. Bound's AMM is purpose-built for trading runes on Bitcoin mainnet.
* **Sequencer**\
  A node operated by Bound and other stakeholders that validates transaction requests, coordinates signing, and broadcasts finalized transactions to Bitcoin.
* **SODAX**\
  The cross-chain swap infrastructure powering Bound's native asset swaps across BTC, ETH, SOL, and more.
* **Trading Wallet**\
  A 2-of-2 timelocked multisig wallet created on signup. Requires both your signature and Bound's co-signature. Enables near-instant trade execution. Bound's signature expires after 3 months.
* **UTXO (Unspent Transaction Output)**\
  The fundamental unit of value in Bitcoin. All Bound AMM operations are built around Bitcoin's UTXO model.
* **Virtual Mint**\
  Bound Launchpad's off-chain minting mechanism that mirrors on-chain mint UX while committing raised BTC to AMM liquidity instead of miner fees.

## FAQ

{% hint style="info" %}
FAQ content to be compiled from team and support channels. See VERIFY log item #46.
{% endhint %}
