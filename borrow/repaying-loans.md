# Repayment And Walk-Away

Borrowers can repay principal plus interest any time during the loan term to reclaim their BTC.

## Repayment

Repayment is handled through an atomic repayment PSBT:

* bUSD moves from the borrower to the lender.
* BTC moves from escrow back to the borrower.
* The repayment transaction settles all legs together or none do.

Once repayment is complete, the loan is closed and the borrower has recovered the escrowed BTC.

## Walk-away

If the borrower does not repay by the end of the grace period, the default path becomes available. The lender can claim the escrowed BTC, and the borrower keeps the bUSD received at origination.

There is no price-based liquidation during the term. The borrower is not forced out of the position because BTC moves below a threshold.

## Interest

The interest paid by the borrower compensates the lender for providing fixed-term bUSD liquidity and accepting the walk-away outcome. The borrower pays for the ability to access bUSD while retaining the choice to repay and reclaim BTC, or walk away at maturity.
