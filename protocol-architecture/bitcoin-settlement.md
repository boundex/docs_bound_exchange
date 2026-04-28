# Bitcoin Settlement

All Bound trades execute and settle on **Bitcoin mainnet** - not a sidechain, not a rollup.

## PSBT-based flow

Every transaction uses **PSBTs (Partially Signed Bitcoin Transactions)**:

1. User requests a quote
2. Bound returns an unsigned PSBT
3. User signs the PSBT with their Trading Wallet key
4. Bound validates the request is still current, then co-signs
5. The fully signed PSBT is broadcast to the Bitcoin network
6. User's balance updates immediately in the Trading Wallet

## Trading Wallet

The Trading Wallet is a **2-of-2 Timelocked Multisig**:

* **User key** - held by the user's device (via passkey)
* **Bound backend key** - held by Bound's signing infrastructure

Both signatures are required for every transaction. This ensures neither party can act unilaterally.

**Timelock:** Bound's co-signature expires after **3 months**. After expiry, users can withdraw funds independently - even if Bound is unavailable. Users are prompted to refresh their Trading Wallet to continue using the platform.

## Sequencer flow

```
User submits BTC raw tx
    ↓
Sequencer 1: validates (sequence number, signature, format, OPCODEs)
    ↓
Peer validation with other sequencers
    ↓
EVM node: executes swap, updates state
    ↓
Bitcoin signature collected
    ↓
Transaction broadcast to Bitcoin network
```
