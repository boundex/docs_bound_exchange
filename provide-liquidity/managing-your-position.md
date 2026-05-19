# Managing Your Position

## Viewing your positions

All active LP positions are visible in the **Positions** tab. Each position shows your current range, asset allocation, and accrued fees.

## Claiming fees

1. Go to the **Portfolio** tab
2. Click **Claim** on the position you want to collect fees from
3. Sign the PSBT when prompted
4. Fees are sent to your Trading Wallet

## Withdrawing liquidity

1. Go to the **Positions** tab
2. Select the position you want to close
3. Click **Withdraw**
4. Sign the PSBT to remove your liquidity
5. Assets are returned to your Trading Wallet

{% hint style="warning" %}
If the AMM quote changes between your request and confirmation, the withdrawal request may be dropped. Simply try again.
{% endhint %}

## In-range vs out-of-range

Your position earns fees only when the current market price is within your selected range.

If the price moves outside your range, your position becomes **out-of-range** and stops earning fees until the price returns within the range.
