# Claimable Receipts

### Overview

In certain situations, a swap may result in a **claimable receipt** instead of immediately transferring the output asset to your wallet.

When this happens, the assets are securely held by the protocol on your behalf, and the Bound application displays your claimable balance. Since the assets have not yet been transferred on-chain to your wallet, blockchain explorers may temporarily show a different balance than the application.

This is expected behavior and does not indicate that your assets are missing.

***

### When Are Claimable Receipts Created?

A claimable receipt may be created when the output assets from a swap cannot be immediately delivered to your wallet during settlement.

Instead of failing the transaction, the protocol records the amount owed to your wallet as a claimable receipt, allowing the swap to complete while preserving your assets.

***

### Why Doesn't My Wallet Show My Tokens?

Blockchain explorers only display assets that currently exist in your wallet.

Assets represented by a claimable receipt are still held by the protocol until they are claimed. As a result, your on-chain balance may temporarily differ from the balance shown in the Bound application.

For example:

```
Wallet (on-chain)
0 XYZ
Bound App
1,940,000 XYZ
```

This simply means your assets are awaiting claim and have not yet been transferred into your wallet.

***

### How Do I Claim My Assets?

Claimable receipts are auto-delivered on the next platform action. No manual claim transaction is required.

***

### Checking Your Claimable Balance

Outstanding claimable receipts can be queried using the Receipt API.

```
GET /api/transactions/receipt/{wallet_address}
```

Example:

```
GET /api/transactions/receipt/bc1...
```

***

### Frequently Asked Questions

#### Are my assets safe?

Yes. Claimable receipts represent assets that are securely held by the protocol and remain associated with your wallet until they are claimed.

#### Why does the application show more tokens than my wallet?

Bound includes both on-chain assets and any outstanding claimable receipts. Blockchain explorers only display assets currently held by your wallet.

#### Did my swap fail?

No. A claimable receipt indicates that the swap completed successfully, but the output assets have not yet been transferred into your wallet.
