# Trade Flow

Creating a Binary Perp follows a deterministic sequence to ensure every position is validated, constructed, and executed safely.

Throughout the process, the user retains full control of their assets and must authorize every action with their wallet.

***

### Prerequisites

Before creating a Binary Perp, the following requirements must be met:

* A funded Hyperliquid account.
* Sufficient USDC to cover the position margin.
* Builder fee approval (required only once per account).

***

### 1. Configure the Position

The user selects:

* Underlying asset
* Upper price
* Lower price
* Prediction (which price will be reached first)
* Position size

Bound validates the submitted parameters before constructing the position.

***

### 2. Position Validation

Before any transactions are prepared, Bound verifies that the position satisfies all protocol requirements.

Validation includes checks such as:

* Position size is within supported limits.
* Price levels are within the supported range.
* The account has sufficient available balance.
* No conflicting Binary Perp already exists for the selected asset.
* The account is eligible to trade.

If any validation fails, no transactions are prepared and the position is rejected.

***

### 3. Position Construction

Once validation succeeds, Bound determines the appropriate perpetual position required to reproduce the selected binary outcome.

This includes calculating:

* Position direction (Long or Short)
* Isolated margin
* Required leverage
* Take-profit level
* Liquidation level

Bound then prepares the required Hyperliquid transactions.

***

### 4. User Authorization

The prepared transactions are presented to the user for review.

Nothing is submitted until the user approves and signs the transactions using their wallet.

Bound never has the ability to execute trades or move assets without explicit user authorization.

***

### 5. Execution

After the transactions are signed:

1. Hyperliquid opens the perpetual position.
2. The take-profit order is created.
3. Bound verifies that the position was successfully opened.

Once verified, the Binary Perp transitions to the **Active** state.

***

### Failed Execution

If the position cannot be opened successfully, the proposal is discarded and no Binary Perp is created.

The user may retry by creating a new proposal using current market conditions.
