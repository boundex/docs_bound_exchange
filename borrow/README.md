# Borrow

Borrow lets BTC holders access bUSD liquidity through no-liquidation term loans.

A borrower locks BTC in a native Bitcoin multisig escrow and receives bUSD from a lender. The loan has a fixed term and fixed rate. During the term, the borrower can repay principal plus interest to reclaim the BTC. If the borrower does not repay by the end of the grace period, the lender can claim the escrowed BTC.

Bound coordinates the loan transaction flow and acts as a co-signer, but Bound never takes custody of borrower or lender assets.
