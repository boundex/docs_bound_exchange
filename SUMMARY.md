# Table of contents

## Boundary Perps

* [Overview](README.md)
* [How Boundary Perps Work](boundary-perps/how-it-works.md)
* [Products](boundary-perps/products/README.md)
  * [Vanilla Boundary Perps](boundary-perps/products/vanilla-barrier-perps.md)
* [Pricing](boundary-perps/pricing/README.md)
  * [Pricing Overview](boundary-perps/pricing/readme-1.md)
  * [Vanilla Barrier Perps Pricing](boundary-perps/pricing/vanilla-barrier-perps.md)
* [Quotes & Opening a Position](boundary-perps/quotes-and-opening.md)
* [Position Lifecycle](boundary-perps/position-lifecycle.md)
* [Settlement & Claims](boundary-perps/settlement-and-claims.md)
* [Closing Early](boundary-perps/closing-early.md)
* [Liquidity & Hedging](boundary-perps/liquidity-and-hedging.md)
* [Risks & Safeguards](boundary-perps/risks-and-safeguards.md)
* [Technical Reference](boundary-perps/technical-reference/README.md)
  * [Pricing Model](boundary-perps/technical-reference/pricing-model.md)
  * [Hedging Model](boundary-perps/technical-reference/hedging-model.md)
  * [LP Accounting & Buffer](boundary-perps/technical-reference/lp-accounting-and-buffer.md)
  * [Settlement Mechanics](boundary-perps/technical-reference/settlement-mechanics.md)
  * [Circuit Breakers](boundary-perps/technical-reference/circuit-breakers.md)
  * [Contract Architecture](boundary-perps/technical-reference/contract-architecture.md)
* [Appendix](boundary-perps/appendix/README.md)
  * [Glossary](boundary-perps/appendix/glossary.md)
  * [Roles](boundary-perps/appendix/roles.md)
* [FAQ](boundary-perps/faq.md)

## Bound Auth

* [What is Bound Auth](bound-auth/what-is-bound-auth.md)
* [How It Works](bound-auth/how-it-works.md)
* [Security Model](bound-auth/security-model.md)
* [Account Settings](bound-auth/account-settings.md)
* [Fund Recovery](fund-recovery/export-private-key.md)

## Getting Started

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

## Developer Guide <a href="#developer-guide-backup" id="developer-guide-backup"></a>

* [API Overview](developer-guide-backup/api-overview.md)
* [Authentication Guide](developer-guide-backup/authentication-guide.md)
* [API Reference](developer-guide-backup/api-reference/README.md)
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
* [Affiliate Fee Integration](developer-guide-backup/affiliate-fee-integration.md)
* [Glossary](developer-guide-backup/glossary.md)

## Developer Guide

* [API Reference](developer-guide/api-reference/README.md)
  * ```yaml
    props:
      models: true
      downloadLink: false
      grouping: by-tag
    type: builtin:openapi
    dependencies:
      spec:
        ref:
          kind: openapi
          spec: barrier-perps
    ```

## Legal

* [Terms and Conditions](legal/terms-and-conditions.md)
* [Privacy Policy](legal/privacy-policy.md)
