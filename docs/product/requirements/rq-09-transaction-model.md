# RQ-09 — Transaction Model

## Status
Proposed

## Priority
P0

## Purpose
Define the core transaction model for the finance module so balances, budgets, imports, recurring items, analytics, and reconciliation all operate on the same ledger rules.

## Scope
This requirement covers:

- transaction kinds
- amount and direction rules
- posting date rules
- memo and note behavior
- refunds and adjustments
- transfer-linked behavior
- acceptance criteria

## Out of Scope
This requirement does not yet include:

- attachments in v1
- OCR receipt scanning
- AI auto-categorization
- full split-transaction support unless explicitly added later

## Product Decision
V1 uses an explicit transaction model that distinguishes ordinary income/expense entries from transfers and adjustments.
A transaction is a durable ledger event, not just a UI row.

## Functional Requirements

### 1. Transaction Kinds
V1 must support at minimum:

- expense transaction
- income transaction
- transfer transaction
- balance adjustment transaction

### 2. Required Base Fields
A transaction must support at minimum:

- stable identifier
- transaction kind
- amount
- effective date
- created timestamp
- updated timestamp
- source account or relevant account context
- optional category where applicable
- optional payee
- optional memo or note
- state as defined by lifecycle requirements

### 3. Amount Rules
- Amount must be stored as a positive monetary quantity in the product model.
- Direction is determined by transaction kind rather than a negative number convention.
- Import flows may accept signed amounts, but they must normalize to the product model consistently.

### 4. Date Rules
The effective transaction date determines:

- account balance ordering
- budget month inclusion
- analytics period inclusion
- reconciliation eligibility

Created and updated timestamps are operational metadata and do not replace the effective date.

### 5. Category Rules
- Expense transactions may use expense categories.
- Income transactions may use income categories.
- Transfers must not require categories.
- Balance adjustments may use a dedicated adjustment reason or note rather than normal spending categories in v1.

### 6. Memo / Note Rules
- A transaction may include a free-text memo or note.
- Notes must not be required for normal entry.
- Notes should survive import/export and sync.

### 7. Refunds
V1 must document a consistent refund policy.
Recommended baseline:

- a refund is stored as an income-like or reversal transaction tied to the relevant account and category context
- refunds should be visible in history and analytics rather than silently netted away

If the first implementation does not create an explicit refund subtype, the product must still document how category spending and reports treat reversals.

### 8. Balance Adjustments
A balance adjustment exists to correct the tracked account state when it does not match reality.
Adjustment entries must:

- be explicit
- be auditable
- affect balance math
- not be silently merged into ordinary history edits

### 9. Transfer Relationship
Transfers are a special transaction kind that move value between two accounts.
The product may represent them internally as linked entries or a single transfer record with two account references, but feature behavior must remain consistent with account and reporting requirements.

### 10. Editing Behavior
- A user must be able to edit ordinary transactions.
- Later lifecycle rules may restrict edits to reconciled transactions.
- Editing must update balances, budgets, and derived summaries deterministically.

### 11. Deletion Behavior
- Deleting a transaction must remove or reverse its effect on balances and summaries.
- If the product later adopts soft-delete semantics for sync safety, deletion must still remain understandable at the user level.
- Reconciled transactions may require stronger warnings before deletion.

### 12. Searchability
The model should support later filtering by:

- date
- account
- category
- payee
- note text
- transaction kind
- state

## Data Considerations
The product model must preserve:

- stable transaction identity
- deterministic balance math
- compatibility with recurring templates and reconciliation checkpoints
- portability through export/import and snapshot sync

## UX Expectations
- The user should not need to think in accounting signs to enter a transaction correctly.
- Transfers and adjustments should have dedicated flows rather than pretending to be ordinary expenses.
- The transaction list should support meaningful display of kind, account, amount, date, and state.

## Edge Cases
- Backdated transactions must affect historical balances and monthly budgets.
- Imported signed amounts must normalize safely.
- Refunds should not accidentally double-count as both negative spending and positive income.
- Deleting a transfer must preserve linkage integrity between both sides if the implementation stores linked entries.

## Acceptance Criteria
- A user can create income, expense, transfer, and adjustment transactions.
- Account balances change deterministically based on transaction kind and date.
- Budget calculations include only applicable posted transactions.
- Transactions support memo text and optional payee/category assignment.
- Editing or deleting a transaction updates dependent summaries consistently.
