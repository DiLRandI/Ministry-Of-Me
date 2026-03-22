# Transaction Lifecycle

## Purpose
This document defines the lifecycle states for finance transactions and how those states interact with balances, budgets, recurring schedules, and reconciliation.

It complements the product requirements for transactions, recurring items, and reconciliation.

## Core States

The model should support at minimum these lifecycle states:

- `scheduled`
- `posted`
- `cleared`
- `reconciled`

A product may hide some states in the first UI pass, but the underlying behavior must remain consistent.

## State Definitions

### Scheduled
A transaction that is expected in the future or generated from a recurring template but is not yet part of the live posted ledger.

Properties:

- does not affect current account balances
- does not affect budget spent totals
- may appear in upcoming views
- may be posted, skipped, edited, or cancelled according to recurring rules

### Posted
A transaction that is part of the active working ledger.

Properties:

- affects account balances
- affects budget totals if category/date rules apply
- appears in analytics for its effective date
- may later become cleared and reconciled

### Cleared
A posted transaction that the user knows has appeared at the external account source or real-world balance source.

Properties:

- still part of the posted ledger
- contributes to reconciliation preparation
- should be distinguishable from merely posted transactions

### Reconciled
A cleared transaction that is included in a completed reconciliation checkpoint.

Properties:

- part of a historical balance-confirmation boundary
- editing or deleting it requires stronger warning or checkpoint invalidation
- should remain visible in account history

## Allowed Transitions

### Scheduled -> Posted
Occurs when the user confirms an expected occurrence as a real transaction.

### Scheduled -> Skipped or Cancelled
A scheduled occurrence may be skipped without deleting the recurring template.
If skipped, the product should preserve enough information to avoid regenerating it incorrectly.

### Posted -> Cleared
Occurs when the user marks the transaction as seen in the external source or confirmed in reality.

### Cleared -> Reconciled
Occurs when a reconciliation checkpoint is successfully completed.

### Posted -> Edited
Edits are allowed, but downstream calculations must update.
Restrictions may apply if the transaction is already reconciled.

### Posted or Cleared -> Deleted
Deletion may be allowed, but it must update balances and summaries consistently.
If the transaction is reconciled, stronger warning or checkpoint invalidation rules apply.

## Invalidating Reconciliation
The following actions on a reconciled transaction should trigger a warning and may invalidate the affected checkpoint:

- amount change
- effective date change
- account change
- deletion
- transfer linkage change where relevant

The product does not need to silently block all such edits, but it must not pretend the checkpoint remains valid if balance history changed.

## Budget Interaction
- `scheduled` transactions do not reduce budget spent totals.
- `posted` transactions reduce budget totals when their category/date rules apply.
- `cleared` and `reconciled` transactions continue to count because they are already posted.

## Balance Interaction
- `scheduled` transactions do not affect current balances.
- `posted`, `cleared`, and `reconciled` transactions affect balances according to transaction kind and effective date.

## Recurring Interaction
Recurring templates generate conceptual future occurrences.
A posted transaction instance derived from a recurring template should remain distinguishable from the template itself.
A skipped occurrence must not accidentally reappear as a normal scheduled item later.

## Transfer Interaction
If transfers are represented as linked ledger entries, lifecycle transitions that apply to one side must keep the pair internally consistent where possible.

Examples:

- posting a transfer posts both sides
- deleting a transfer removes or reverses both sides consistently
- reconciliation may occur per account, so each side may be viewed through its account context while still preserving linkage

## Import Interaction
Imported transactions usually enter as `posted` unless the import flow explicitly supports other states.
The import format documentation should state this clearly.

## UI Guidance
The user should be able to understand:

- which items are future expectations
- which items are ordinary ledger entries
- which items are already confirmed against reality

The UI does not need to expose all internal mechanics at once, but it must not blur them in a way that breaks product trust.
