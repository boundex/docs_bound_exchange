# Swap Runes

Bound's Runes AMM enables native Bitcoin token swaps with near-instant execution - multiple swaps within a single block, settling at end of block.

## How a swap works

1. Enter the asset and amount you want to swap
2. Bound displays a quote
3. If satisfied, confirm the swap - your wallet signs a **PSBT** (Partially Signed Bitcoin Transaction)
4. Bound checks the quote is still valid, then co-signs and broadcasts the transaction
5. Your updated balance is immediately visible in your Trading Wallet

{% hint style="warning" %}
Quotes are valid at the moment of request. If another user's activity changes the price before you confirm, the quote may expire and your trade will be dropped. Simply request a new quote and try again.
{% endhint %}

## Settlement

Trades execute on **Bitcoin mainnet**. Settlement occurs at the end of the block - your balance updates immediately in your Trading Wallet, with final on-chain confirmation following block mining.
