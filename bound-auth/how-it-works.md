# How It Works

## Authentication flow

1. **Sign up** - You create a passkey or password on your device
2. **Wallet creation** - Bound generates self-custodial wallets for BTC, EVM, and Solana
3. **Sign transactions** - When you trade or interact with the platform, your passkey signs transactions locally on your device
4. **No keys leave your device** - Private keys never leave your device at any point

## Trading Wallet

When you first connect, Bound creates a **Trading Wallet** - a 2-of-2 timelocked multisig wallet where:

* One signature comes from your passkey (your device)
* One signature comes from the Bound backend

This setup enables near-instant trade execution while maintaining security. If Bound's signature expires (after 3 months), you can always withdraw your funds independently - even if Bound is unavailable.

{% hint style="info" %}
**VERIFY** - Detailed passkey key generation, storage, session management, and multi-device support details to be confirmed with engineering team.
{% endhint %}
