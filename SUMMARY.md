# Table of contents

* [Getting Started](README.md)
  * [Platform Overview](getting-started/platform-overview.md)
  * [Create Your Account](getting-started/create-your-account.md)
  * [Connect An Existing Wallet](getting-started/connect-an-existing-wallet.md)
  * [Supported Assets And Chains](getting-started/supported-assets-and-chains.md)
* [Bound-Auth](bound-auth/README.md)
  * [What is Bound-Auth](bound-auth/what-is-bound-auth.md)
  * [How It Works](bound-auth/how-it-works.md)
  * [Security Model](bound-auth/security-model.md)
  * [Account Settings](bound-auth/account-settings.md)
* [Fund Recovery](fund-recovery/README.md)
  * [Export Private Key](fund-recovery/export-private-key.md)
* [Trade](trade/README.md)
  * [Trading Interface Overview](trade/trading-interface-overview.md)
  * [Swap Runes](trade/swap-runes.md)
  * [Cross Chain Swaps](trade/cross-chain-swaps.md)
  * [Pricing And Fees](trade/pricing-and-fees.md)
  * [PNL And Average Entry](trade/pnl-and-average-entry.md)
* [Provide-Liquidity](provide-liquidity/README.md)
  * [How It Works](provide-liquidity/how-it-works.md)
  * [Setting Your Range](provide-liquidity/setting-your-range.md)
  * [Managing Your Position](provide-liquidity/managing-your-position.md)
  * [Risk Considerations](provide-liquidity/risk-considerations.md)
* [Launchpad](launchpad/README.md)
  * [Overview](launchpad/overview.md)
  * [Create A Token](launchpad/create-a-token.md)
  * [Diamond Hands](launchpad/diamond-hands.md)
  * [Virtual Mint Mechanics](launchpad/virtual-mint-mechanics.md)
* [Borrow](borrow/README.md)
  * [Overview](borrow/overview.md)
  * [Liquidation Free Loans](borrow/liquidation-free-loans.md)
  * [Overcollateralized Loans](borrow/overcollateralized-loans.md)
  * [Repaying Loans](borrow/repaying-loans.md)
* [Protocol-Architecture](protocol-architecture/README.md)
  * [Architecture Overview](protocol-architecture/architecture-overview.md)
  * [Runes AMM Technical](protocol-architecture/runes-amm-technical.md)
  * [Bitcoin Settlement](protocol-architecture/bitcoin-settlement.md)
  * [Sodax Integration](protocol-architecture/sodax-integration.md)
  * [bUSD Technical Spec](protocol-architecture/busd-technical-spec.md)
* [Developer-Guide](developer-guide/README.md)
  * [API Overview](developer-guide/api-overview.md)
  * [Authentication Guide](developer-guide/authentication-guide.md)
  * [API Reference](developer-guide/api-reference/README.md)
    * ```yaml
      type: builtin:openapi
      props:
        models: true
        downloadLink: false
      dependencies:
        spec:
          ref:
            kind: openapi
            spec: api-bound
      ```
  * [Affiliate Fee Integration](developer-guide/affiliate-fee-integration.md)
  * [Glossary](developer-guide/glossary.md)
