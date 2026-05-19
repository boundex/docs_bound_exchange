# Risk Considerations

## Price range risk

Custom ranges act as a DCA (dollar-cost averaging) mechanism. As price moves through your range, your position automatically shifts between the two assets. If price moves outside your range entirely, your position becomes 100% in one asset and stops earning fees until price returns to range.

Tighter ranges earn more fees per unit of capital when price stays within range - but carry higher risk of going out of range.

## Quote expiry

If another user's activity changes the pool state between your request and confirmation, your AMM request will be dropped. You'll need to request a new quote and resubmit. This is a feature, not a bug - it ensures you always transact at a fair price.
