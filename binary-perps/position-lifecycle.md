# Position Lifecycle

Every Binary Perp progresses through a well-defined lifecycle, from creation to settlement.

Understanding these states helps explain the current status of your position and how it reaches its final outcome.

***

### Lifecycle Overview

```mermaid
stateDiagram-v2
    [*] --> Proposed

    Proposed --> Active : Position Opened
    Proposed --> Aborted : Proposal Expired / Abandoned

    Active --> ResolvedWon : Take Profit Reached
    Active --> ResolvedLost : Liquidated
    Active --> ResolvedCashedOut : User Cash-Out
    Active --> Voided : External Modification

    ResolvedWon --> [*]
    ResolvedLost --> [*]
    ResolvedCashedOut --> [*]
    Voided --> [*]
    Aborted --> [*]

    state ResolvedWon {
        [*] --> Won
    }

    state ResolvedLost {
        [*] --> Lost
    }

    state ResolvedCashedOut {
        [*] --> CashedOut
    }
```

***

### Lifecycle States

| State                     | Description                                                                                                                        |
| ------------------------- | ---------------------------------------------------------------------------------------------------------------------------------- |
| **Proposed**              | The position has been validated and prepared for signing but has not yet been submitted to Hyperliquid. No market exposure exists. |
| **Active**                | The position has been successfully opened on Hyperliquid and is actively monitored by Bound.                                       |
| **Resolved – Won**        | The selected price level was reached first and the position closed in profit.                                                      |
| **Resolved – Lost**       | The opposite price level was reached first, resulting in liquidation.                                                              |
| **Resolved – Cashed Out** | The position was closed early by the user before either price level was reached.                                                   |
| **Voided**                | The position was modified outside of Bound and can no longer be tracked as a Binary Perp.                                          |
| **Aborted**               | The position was never opened because the proposal expired or the signing process was abandoned.                                   |

***

### Proposed

A Binary Perp enters the **Proposed** state after Bound validates the position parameters and prepares the required transactions.

At this stage:

* No orders have been executed.
* No market exposure exists.
* The user may review the position before signing.

If the proposal expires before being signed, it transitions to **Aborted**.

***

### Active

Once the user signs and the position is successfully opened on Hyperliquid, the Binary Perp becomes **Active**.

While active, Bound continuously monitors the position for:

* Take-profit execution
* Liquidation
* Early cash-out
* External modifications

The position remains active until it transitions into one of the terminal states below.

***

### Resolved – Won

The selected price level was reached first, causing the position to close in profit.

***

### Resolved – Lost

The opposite price level was reached first, causing the position to be liquidated.

***

### Resolved – Cashed Out

The user voluntarily closed the position before either price level was reached.

***

### Voided

The position was modified outside of Bound—for example, by manually closing the position, cancelling orders, or adjusting margin directly on Hyperliquid.

Once this occurs, Bound can no longer guarantee that the position follows the Binary Perp rules, so the position is marked as **Voided**.

***

### Aborted

The proposal expired or the signing process was abandoned before the position was opened.

Since no position was ever created, no market exposure existed.
