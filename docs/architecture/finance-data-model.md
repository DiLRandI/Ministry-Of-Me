# Finance Data Model

## Purpose
This document bridges the product requirements and the implementation architecture by describing the core finance entities, their relationships, and the main invariants they must preserve.

It is intentionally implementation-facing, but it remains at the conceptual model level rather than committing to exact Drift code.

## Core Entities

### Account
Represents a store of value or liability tracked by the user.

Key attributes:

- `id`
- `name`
- `type`
- `openingBalance`
- `openingBalanceDate`
- `isArchived`
- `createdAtUtc`
- `updatedAtUtc`
- `deletedAtUtc` if soft delete is used

### Category
Represents a reusable classification for income or expense transactions.

Key attributes:

- `id`
- `name`
- `type` (`income` or `expense`)
- `groupId` or parent label if grouping is used
- `sortOrder`
- `isArchived`
- `createdAtUtc`
- `updatedAtUtc`
- `deletedAtUtc`

### CategoryGroup
Optional grouping layer used for UI organization and reporting.

Key attributes:

- `id`
- `name`
- `sortOrder`
- `createdAtUtc`
- `updatedAtUtc`
- `deletedAtUtc`

### Payee
Represents the merchant, provider, or counterparty linked to a transaction.

Key attributes:

- `id`
- `name`
- `isArchived`
- `createdAtUtc`
- `updatedAtUtc`
- `deletedAtUtc`

### Transaction
Represents a durable ledger event.

Key attributes:

- `id`
- `kind` (`expense`, `income`, `transfer`, `adjustment`)
- `amountMinor` or equivalent exact money representation
- `effectiveAt`
- `accountId`
- `categoryId` where applicable
- `payeeId` where applicable
- `note`
- `state`
- `createdAtUtc`
- `updatedAtUtc`
- `deletedAtUtc`

### TransferLink
If transfers are stored as linked ledger rows instead of a single dual-account record, this relation preserves linkage.

Key attributes:

- `id`
- `sourceTransactionId`
- `destinationTransactionId`
- `createdAtUtc`

This entity may be unnecessary if transfers are modeled differently, but the conceptual requirement remains: the two sides of a transfer must remain consistently linked.

### BudgetMonth
Represents the monthly budget container.

Key attributes:

- `id`
- `year`
- `month`
- `createdAtUtc`
- `updatedAtUtc`

### BudgetEntry
Represents a category budget amount for a given month.

Key attributes:

- `id`
- `budgetMonthId`
- `categoryId`
- `budgetAmountMinor`
- `createdAtUtc`
- `updatedAtUtc`
- `deletedAtUtc`

### RecurringTemplate
Represents a recurring future financial event definition.

Key attributes:

- `id`
- `title`
- `kind`
- `amountMinor`
- `accountId`
- `categoryId`
- `payeeId`
- `scheduleRule`
- `startDate`
- `endDate`
- `isActive`
- `createdAtUtc`
- `updatedAtUtc`
- `deletedAtUtc`

### RecurringOccurrenceOverride
Represents exceptions to the generated recurring schedule.

Key attributes:

- `id`
- `templateId`
- `occurrenceDate`
- `status` (`scheduled`, `skipped`, `posted`, `modified`)
- `transactionId` if posted
- override fields where needed

### ReconciliationCheckpoint
Represents a completed account reconciliation point.

Key attributes:

- `id`
- `accountId`
- `effectiveDate`
- `targetEndingBalanceMinor`
- `completedAtUtc`
- optional note

### ImportSession
Represents a user-triggered import operation for traceability.

Key attributes:

- `id`
- `sourceType`
- `startedAtUtc`
- `completedAtUtc`
- `status`
- optional source metadata

### SyncState
Represents local metadata about sync status.

Key attributes:

- `lastSnapshotId`
- `lastSyncCompletedAtUtc`
- `lastRemoteDeviceId`
- `localDirtySinceUtc`
- local schema/app version metadata

## High-Level Relationships

- One `Account` has many `Transaction` records.
- One `Category` may be referenced by many `Transaction` records and many `BudgetEntry` records.
- One `Payee` may be referenced by many `Transaction` records and many `RecurringTemplate` records.
- One `BudgetMonth` has many `BudgetEntry` records.
- One `RecurringTemplate` may generate many conceptual occurrences and optionally many override records.
- One `Account` may have many `ReconciliationCheckpoint` records.

## Required Invariants

### Identity and Sync
- Every syncable top-level entity must use a stable text identifier.
- Renames must not change identity.
- Archive operations must not break historical references.

### Ledger Safety
- Transactions are the primary source for balance math.
- Transfers must remain internally linked and balanced.
- Reconciled history must not be silently mutated.

### Budget Safety
- Budget totals for a month must be derivable from budget entries and posted transactions within that month.
- Archived categories must remain resolvable for historical budget displays.

### Recurring Safety
- Templates and posted transaction instances must remain distinguishable.
- Skipping or modifying one occurrence must not destroy the whole series.

### Restore / Sync Safety
- Local sync metadata must stay separate from business-domain rows where practical.
- Snapshot import must preserve referential integrity across the full dataset.

## Suggested Implementation Notes

- Use exact money storage in minor units or another exact decimal-safe representation.
- Prefer explicit enum-like values for account type, category type, transaction kind, and transaction state.
- Use soft deletion where sync safety requires recovery of deleted rows.
- Derived analytics may use SQL views, cached tables, or both, but the transaction ledger remains the canonical basis.

## Out of Scope
This document does not commit to:

- exact Drift table names
- exact index definitions
- exact foreign-key strategies
- exact migration ordering

Those belong in implementation once the product model is accepted.
