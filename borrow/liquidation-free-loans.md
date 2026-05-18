# Loan Lifecycle

Bound term loans are coordinated through PSBTs and enforced by Bitcoin Script.

## Origination

At origination, the borrower, lender, and Bound sign a single Bitcoin transaction. The transaction coordinates the loan legs together:

* The borrower locks BTC into escrow.
* The lender provides bUSD.
* The borrower receives the agreed bUSD loan amount.
* Any origination fee is settled as part of the same transaction.

Because origination is atomic, either every transfer settles together or the transaction does not complete.

## Escrow

The BTC collateral sits in a 2-of-2 multisig escrow with a timelock. The escrow has two spending paths:

| Path | When it applies | Required signer | Result |
| --- | --- | --- | --- |
| Repayment path | During the term and grace period | Borrower plus Bound or lender | Borrower repays and receives BTC back |
| Default path | After the grace period | Lender | Lender claims the BTC collateral |

During the term, the borrower must be a signer for the BTC to move. Bound cannot move collateral by itself.

## Resolution

There are two ways a loan resolves:

* **Repayment** - the borrower repays principal plus interest and receives the escrowed BTC back.
* **Walk-away** - the borrower does not repay by the end of the grace period, keeps the borrowed bUSD, and the lender claims the BTC.

The walk-away outcome is what removes price-based liquidation from the loan. The loan state is based on time and repayment, not BTC market price.
