# RQ-04 — Reconciliation

## Status
Proposed

## Priority
P0

## Purpose
Define how the user verifies that tracked account balances match an external statement or known real-world balance.

This is a core finance workflow because balance accuracy degrades over time without a clear reconciliation model.
The current documentation does not yet define reconciliation behavior.

## Scope
This requirement covers:

- transaction state concepts needed for reconciliation
- cleared and reconciled behavior
- reconciliation sessions against a statement or known balance
- mismatch handling
- acceptance criteria for reconciliation flows

## Out of Scope
This requirement does not yet include:

- automatic statement import
- OCR of bank statements
- institution connectivity
- collaborative reconciliation by multiple users

## Product Decision
V1 should support a lightweight reconciliation workflow with explicit transaction states and a known-balance check.
This gives the user confidence in account accuracy without requiring bank connectivity.

## Functional Requirements

### 1. Transaction States Relevant to Reconciliation
The product model must distinguish at least:

- pending or future
- posted
- cleared
- reconciled

Definitions:

- `pending or future`: not yet part of the confirmed account ledger
- `posted`: included in the working account ledger
- `cleared`: known to have appeared at the institution or real-world account source
- `reconciled`: explicitly accepted as part of a completed reconciliation checkpoint

A product may combine `posted` and `cleared` in the first UI pass if needed, but the model should keep room to separate them.

### 2. Reconciliation Target
A reconciliation must be performed against:

- a selected account
- a statement ending date or effective date
- a target ending balance

The workflow may optionally include a label such as statement month or note.

### 3. Included Transactions
A reconciliation session must evaluate transactions for the selected account up to the reconciliation effective date.
The system should allow the user to mark eligible transactions as cleared during the process.

### 4. Match Rule
A reconciliation is successful when:

`reconciled opening point + cleared net movement up to date = target ending balance`

The implementation may compute this using stored checkpoints or derived ledger math, but the user-facing result must be deterministic.

### 5. Reconciliation Completion
When a reconciliation completes successfully:

- eligible selected transactions are marked reconciled
- a reconciliation checkpoint is stored for the account
- the account gains a last reconciled date and/or balance reference

### 6. Mismatch Handling
If the calculated balance does not equal the target ending balance:

- the system must show the difference clearly
- the system must not silently force completion
- the user may cancel, continue reviewing, or later use an adjustment workflow if that is their chosen correction path

### 7. Editing Reconciled Transactions
- Editing or deleting a reconciled transaction should be restricted or require an explicit warning.
- If such an edit is allowed, the system must mark the affected account as needing reconciliation again or otherwise invalidate the relevant checkpoint.

### 8. Relationship to Balance Adjustments
A reconciliation mismatch should not automatically create a balance adjustment.
If the user chooses to resolve a mismatch through an adjustment, that must be a separate explicit action.

### 9. Account Eligibility
Not every account type must expose reconciliation in the first UI pass, but the model should at least support it for:

- bank accounts
- cash accounts
- credit card accounts

Other account types may opt in later based on use case.

## Data Considerations
The product model must preserve:

- transaction state transitions
- reconciliation checkpoints by account and date
- ability to detect when later edits invalidate a prior checkpoint
- local-only operation without requiring a remote service

## UX Expectations
- Reconciliation should be understandable to a non-accountant.
- The user must be able to see which transactions are contributing to the current difference.
- A completed reconciliation should provide visible confidence that the account is aligned with reality.

## Edge Cases
- A late-entered historical transaction before the last reconciliation date may invalidate a checkpoint.
- Transfers must reconcile correctly because they still affect account balances even if they do not affect expense reports.
- Duplicate transactions can produce mismatches and should be easy to spot in the account ledger.

## Acceptance Criteria
- A user can select an account and enter a target ending balance for a chosen date.
- The system can compare ledger results against that target and show any difference.
- A successful reconciliation creates a persistent checkpoint.
- Reconciled transactions are distinguishable from ordinary posted transactions.
- Editing a reconciled transaction triggers a warning or invalidates the checkpoint consistently.

## Follow-on Questions
- Should the first implementation expose both `cleared` and `reconciled`, or only `reconciled` in the UI while keeping room for `cleared` in the model?
- Should v1 include a dedicated account ledger screen to support reconciliation efficiently?
- Should cash accounts support a simpler “count cash and confirm” reconciliation variant?
