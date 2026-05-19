# Architecture Overview

Bound is built on a layered stack where each layer depends on the one below.

## Stack overview

```
┌─────────────────────────────────────┐
│  Layer 3 — Products                 │
│  Launchpad · Lend/Borrow · Options  │
├─────────────────────────────────────┤
│  Layer 2 — Trading Engine           │
│  Runes AMM · SODAX Crosschain       │
├─────────────────────────────────────┤
│  Layer 1 — Infrastructure           │
│  Bound Auth · Bound APIs            │
└─────────────────────────────────────┘
```

## Smart contract stack

Bound uses an EVM environment for AMM calculations, connected to Bitcoin settlement:

```
Client request
    ↓
Sequencer (validate + relay)
    ↓
Relay Contract
    ↓
Bitcoinstate Contract (tracks LP positions, parses to Uniswap format)
    ↓
Uniswap V3 Contract (executes liquidity operations)
    ↓
Bitcoin Node (signs + broadcasts BTC transaction)
```

## Sequencer nodes

Sequencers are nodes operated by Bound and other stakeholders. They:

1. Validate incoming requests (sequence numbers, signatures, data formats, OPCODEs)
2. Forward validated requests to other validator nodes
3. Update state on the EVM chain
4. Co-sign the Bitcoin transaction
5. When consensus is reached, broadcast the final BTC transaction to mainnet
