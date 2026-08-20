# Roles

## User

The user:

* Requests and accepts position quotes.
* Deposits the stake and protocol fee.
* Selects the product parameters and outcome.
* May request and accept an early-close buyback.
* Owns any winning or buyback proceeds.

The user's maximum position loss is the stake, excluding separately charged fees.

## Bound Book Contract

The book contract is the counterparty of record. It:

* Quotes and opens positions.
* Holds stakes and buffer assets.
* Maintains the active position book.
* Posts and recalls HyperCore collateral.
* Calculates and executes the net hedge target.
* Stores authenticated mark readings.
* Settles positions and pays claims or buybacks.
* Enforces capacity limits, outflow limits, and circuit breakers.

## Fee-Handler Contract

The fee-handler receives protocol fees swept from the book contract. Bound can claim the accumulated fees according to the handler's authorization rules.

The handler does not hold assets backing positions or LP shares.

## Liquidity Provider

An LP deposits USDC buffer capital and receives shares priced at the book's NAV per share. LPs:

* Supply hedge margin beyond user stakes.
* Absorb hedge and cost variance.
* Earn the spread through changes in NAV.
* Withdraw subject to minimum-buffer, outflow, and breaker restrictions.

LP access may be permissioned at launch.

## Hyperliquid

Hyperliquid provides:

* HyperEVM execution for the Bound contracts.
* HyperCore mark prices used for quoting and settlement.
* Perpetual markets used for protocol hedging.
* The HyperCore account where hedge collateral and positions are maintained.

The design assumes Hyperliquid and its precompiles perform as specified.

## Settlement Keeper

The keeper monitors active positions and submits `settlePosition` calls when boundary evidence exists. Settlement is permissionless, so the keeper improves promptness but does not hold exclusive authority.

## Mark Worker

The mark worker periodically calls the contract to read the HyperCore mark precompile and store the result. Publishing is permissionless and cannot forge a price because the contract reads the precompile itself.

## Bound Operator

Bound operates reference mark-worker and settlement-keeper services, monitors book and breaker health, manages authorized configuration changes, and may invoke the admin pause when required.

Operational authority does not allow Bound to change an accepted position's boundaries or payout.
