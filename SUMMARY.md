# Table of contents

## Getting Started

* [Overview](README.md)
* [Create Your Account](getting-started/create-your-account.md)
* [Connect An Existing Wallet](getting-started/connect-an-existing-wallet.md)
* [Supported Assets And Chains](getting-started/supported-assets-and-chains.md)

## Trade

* [Trading Interface Overview](trade/trading-interface-overview.md)
* [Swap Runes](trade/swap-runes.md)
* [Cross Chain Swaps](trade/cross-chain-swaps.md)
* [Pricing And Fees](trade/pricing-and-fees.md)
* [PNL And Average Entry](trade/pnl-and-average-entry.md)
* [Slippage & Claimable Receipts](trade/slippage-and-claimable-receipts.md)

## Provide Liquidity

* [How It Works](provide-liquidity/how-it-works.md)
* [Setting Your Range](provide-liquidity/setting-your-range.md)
* [Managing Your Position](provide-liquidity/managing-your-position.md)
* [Risk Considerations](provide-liquidity/risk-considerations.md)

## Borrow

* [Overview](borrow/overview.md)
* [Loan Lifecycle](borrow/liquidation-free-loans.md)
* [Repayment And Walk-Away](borrow/repaying-loans.md)
* [Trust And Custody Model](borrow/trust-and-custody-model.md)

## Launchpad

* [Overview](launchpad/overview.md)
* [Create A Token](launchpad/create-a-token.md)
* [Diamond Hands](launchpad/diamond-hands.md)
* [Virtual Mint Mechanics](launchpad/virtual-mint-mechanics.md)

## Protocol Architecture

* [Architecture Overview](protocol-architecture/architecture-overview.md)
* [Runes AMM](protocol-architecture/runes-amm-technical.md)
* [Bitcoin Settlement](protocol-architecture/bitcoin-settlement.md)
* [Sodax Integration](protocol-architecture/sodax-integration.md)
* [bUSD Technical Spec](protocol-architecture/busd-technical-spec.md)

## Barrier Perps

* [Overview](barrier-perps/README.md)
* [How Barrier Perps Work](barrier-perps/how-it-works.md)
* Products
  * [Vanilla Barrier Perps](barrier-perps/products/vanilla-barrier-perps.md)
* Pricing
  * [Pricing Overview](barrier-perps/pricing/README.md)
  * [Vanilla Barrier Perps Pricing](barrier-perps/pricing/vanilla-barrier-perps.md)
* [Quotes & Opening a Position](barrier-perps/quotes-and-opening.md)
* [Position Lifecycle](barrier-perps/position-lifecycle.md)
* [Settlement & Claims](barrier-perps/settlement-and-claims.md)
* [Closing Early](barrier-perps/closing-early.md)
* [Liquidity & Hedging](barrier-perps/liquidity-and-hedging.md)
* [Risks & Safeguards](barrier-perps/risks-and-safeguards.md)
* Technical Reference
  * [Pricing Model](barrier-perps/technical-reference/pricing-model.md)
  * [Hedging Model](barrier-perps/technical-reference/hedging-model.md)
  * [LP Accounting & Buffer](barrier-perps/technical-reference/lp-accounting-and-buffer.md)
  * [Settlement Mechanics](barrier-perps/technical-reference/settlement-mechanics.md)
  * [Circuit Breakers](barrier-perps/technical-reference/circuit-breakers.md)
  * [Contract Architecture](barrier-perps/technical-reference/contract-architecture.md)
* Appendix
  * [Configuration Parameters](barrier-perps/appendix/configuration-parameters.md)
  * [Glossary](barrier-perps/appendix/glossary.md)
  * [Roles](barrier-perps/appendix/roles.md)
* [FAQ](barrier-perps/faq.md)

## Bound Auth

* [What is Bound Auth](bound-auth/what-is-bound-auth.md)
* [How It Works](bound-auth/how-it-works.md)
* [Security Model](bound-auth/security-model.md)
* [Account Settings](bound-auth/account-settings.md)
* [Fund Recovery](fund-recovery/export-private-key.md)

## Developer Guide

* [API Overview](developer-guide/api-overview.md)
* [Authentication Guide](developer-guide/authentication-guide.md)
* [API Reference](developer-guide/api-reference/README.md)
  * ```yaml
    props:
      models: true
      downloadLink: false
    type: builtin:openapi
    dependencies:
      spec:
        ref:
          kind: openapi
          spec: api-bound
    ```
* [Affiliate Fee Integration](developer-guide/affiliate-fee-integration.md)
* [Glossary](developer-guide/glossary.md)

## Legal

* [Terms and Conditions](legal/terms-and-conditions.md)
* [Privacy Policy](legal/privacy-policy.md)
