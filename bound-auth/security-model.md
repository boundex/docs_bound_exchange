# Security Model

## Self-custody guarantee

You always control your own keys. Bound cannot move your funds without your signature.

The Trading Wallet uses a 2-of-2 timelocked multisig structure:

* Your signature is required for every transaction
* Bound's co-signature expires after **3 months**
* After expiry, you can withdraw independently - no Bound involvement needed

This means even in a worst-case scenario where Bound is unavailable, your funds are always recoverable.
