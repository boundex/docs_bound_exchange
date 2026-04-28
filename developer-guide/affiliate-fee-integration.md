# Affiliate Fee Integration

Partners can earn a commission on swap transactions by passing affiliate fee parameters in API calls.

## How it works

The affiliate fee is deducted from `amountIn` **before** the swap calculation. The pool receives the remainder.

```
affiliateFeeAmount = floor((amountIn × partnerFeeBps) / 1,000,000)
poolAmountIn = amountIn - affiliateFeeAmount
```

## Request example

```json
{
  "type": "SWAP",
  "params": {
    "tokens": ["2584333:39", "0:0"],
    "amountIn": "1000000",
    "amountOut": "100000",
    "feeRates": [3000],
    "isExactIn": true,
    "partnerFeeBps": 10000,
    "partnerFeeRecipient": "bc1q..."
  }
}
```

## Rules

* `partnerFeeBps` and `partnerFeeRecipient` must always be provided together
* **RUNE → BTC swaps:** affiliate receives RUNE. Minimum fee: 1 RUNE
* **BTC → RUNE swaps:** affiliate receives BTC. Minimum fee: 546 sats (auto-adjusted if lower)
* `partnerFeeRecipient` cannot be the pool address or the user's trading address

## Fee calculation examples

**1% fee on a RUNE → BTC swap:**

```
amountIn = 1,000,000 RUNE
affiliateFeeAmount = floor((1,000,000 × 10,000) / 1,000,000) = 10,000 RUNE
poolAmountIn = 990,000 RUNE
```

**0.5% fee on a BTC → RUNE swap:**

```
amountIn = 1,000,000 sats
affiliateFeeAmount = floor((1,000,000 × 5,000) / 1,000,000) = 5,000 sats
poolAmountIn = 995,000 sats
```

## Common errors

| Error             | Cause                                                  | Fix                                    |
| ----------------- | ------------------------------------------------------ | -------------------------------------- |
| Missing recipient | `partnerFeeBps` provided without `partnerFeeRecipient` | Always provide both together           |
| Invalid recipient | Recipient is pool or user address                      | Use a different recipient address      |
| Fee too small     | Calculated fee < 1 RUNE                                | Increase `partnerFeeBps` or `amountIn` |
| Fee too large     | `poolAmountIn` would be 0                              | Reduce `partnerFeeBps`                 |
