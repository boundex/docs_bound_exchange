# Settlement Mechanics

This page defines the detailed first-touch settlement rules for Vanilla Boundary Perps.

## Settlement Inputs

The contract can read current HyperCore state through precompiles, but it cannot query arbitrary historical prices. Settlement therefore uses two sources of onchain evidence:

1. The current mark price read from the HyperCore precompile inside the settle call.
2. Mark readings previously stored by the permissionless mark worker.

For one position:

```text
Upper touch: mark >= upper boundary
Lower touch: mark <= lower boundary
```

## Position Lifetime

Only readings timestamped at or after the position's activation count. Historical readings from before the acceptance transaction are ignored.

This prevents a position from settling against a boundary crossing that occurred before the position existed.

## Settlement Flow

Anyone may call:

```text
settlePosition(positionID)
```

The contract then:

1. Loads the active position and its activation time.
2. Reads the current mark inside the call.
3. Checks eligible stored mark-worker readings.
4. Determines whether either boundary has valid touch evidence.
5. Selects the earliest valid touch if more than one exists.
6. Resolves the position within the same call.
7. Removes its exposure from the active book.
8. Recalculates the hedge target and recalls collateral as appropriate.

## Precedence Rules

If multiple touch readings exist:

1. The earliest timestamp governs.
2. If readings share a timestamp, their publication order within the block governs.

The chosen boundary crossing first produces **Resolved — Won**. The non-chosen boundary crossing first produces **Resolved — Lost**. If neither boundary has crossed, the position remains active.

## Mark Worker

At every publish interval, the mark worker calls a contract entry point. That function reads the HyperCore mark precompile inside the transaction and stores the price and timestamp.

The publisher does not provide the price value itself, so it cannot forge a mark. It can only submit or fail to submit the onchain reading.

The publish entry point is permissionless. Bound operates a reference worker, but any participant can run one.

Stored readings must:

* Include the mark price and timestamp.
* Preserve publication order when timestamps match.
* Remain accessible to settlement for as long as they can affect an active position.

If no reading is stored within the configured number of publish intervals, the mark-worker-staleness breaker restricts quoting, opening, and settlement until clean publishing resumes.

## Idempotency and Scope

Settlement is keyed to one `positionID` and resolves only that position.

Calling settle on an uncrossed or already resolved position is a safe no-op. This allows keepers and independent callers to retry without risking duplicate settlement.

## Claims

A winning payout is delivered through a separate permissionless claim. Anyone may submit the call, but the funds always go to the position's recorded owner.

Resolved but unclaimed payouts remain fully counted in marked liabilities.

## Delays and Freeze

A crossing captured by an in-call or stored reading remains settleable after a delay. Readings published during Freeze preserve the crossing for settlement after the protocol resumes.

Settlement is available under Restricted modes except when mark-worker staleness prevents the protocol from proving first-touch order. Settlement halts during Freeze.

## Observation Floor

The mark-worker publish cadence defines the finest historical observation interval. A brief crossing that occurs and reverses between observable readings may not register. An in-call reading can capture a crossing between scheduled publications, but users should not assume continuous tick-by-tick observation.
