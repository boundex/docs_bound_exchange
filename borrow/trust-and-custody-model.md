# Trust And Custody Model

Bound term loans are designed around self-custody and multi-party signing.

## Borrower-required spending

During the term, the BTC escrow cannot be spent without the borrower's signature. The borrower remains a required participant in any repayment-path movement of the collateral.

## Bound cannot act alone

Bound acts as a PSBT coordinator and co-signer. Bound cannot unilaterally move BTC collateral or disburse lender funds at any point in the loan lifecycle.

## Enforced by Bitcoin Script

The repayment path, default path, and time-based path switch are enforced by Bitcoin Script. They do not depend on Bound servers making a discretionary liquidation decision.

## Native Bitcoin collateral

Collateral stays in a native Bitcoin multisig address throughout the loan lifecycle. There is no wrapping, bridging, or custodial intermediary.

## Lender integration

Lenders connect through an API and sign PSBTs. The lender and co-signer keys can sit behind the custody and key-management controls each counterparty already operates.
