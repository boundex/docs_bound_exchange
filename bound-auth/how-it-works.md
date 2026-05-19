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

## Key storage

When you register, an encrypted copy of your keystore is stored on Bound's servers. This copy can only be decrypted by your passkey — the decryption key never leaves your device.

## Multi-device support

You can add additional devices to your account from the **Account Settings** page. The new device receives an encrypted copy of your keystore via a secure device-to-device handshake — Bound never has access to the unencrypted keys at any point.

Removing a device immediately revokes all of its active sessions.

## Session management

Every login creates a session tied to your device. You can view and revoke active sessions at any time from **Account Settings → Active Sessions**. Use **Log out all other devices** to revoke all sessions except your current one.
