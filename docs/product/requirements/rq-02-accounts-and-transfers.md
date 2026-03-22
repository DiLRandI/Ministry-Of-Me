# RQ-02 — Accounts and Transfers

## Status
Proposed

## Priority
P0

## Purpose
Define the account model and transfer semantics for the finance module so balances, transaction flows, and reporting remain internally consistent.

The current v1 scope mentions accounts as part of the storage model, but it does not yet define what kinds of accounts exist or how money movement between accounts should behave.

## Scope
This requirement covers:

- supported account types for v1
- opening balances
- active and archived account behavior
- balance calculations
- transfers between accounts
- balance adjustments and corrections
- acceptance criteria for account and transfer behavior

## Out of Scope
This requirement does not yet include:

- investment portfolio accounting
- loans with amortization schedules
- foreign exchange handling
- institution sync or bank connectivity
- shared account ownership between multiple users

## Product Decision
V1 must support a small, explicit account model that is enough for personal finance tracking without introducing full double-entry accounting complexity in the UI.

## Functional Requirements

### 1. Supported Account Types
V1 must support at least these account types:

- cash
- bank account
- credit card
- liability
- asset

Definitions:

- `cash`: physical cash or cash-equivalent wallet balances
- `bank account`: checking, savings, or similar deposit accounts
- `credit card`: revolving card balance account
- `liability`: money owed outside of a credit card model
- `asset`: a tracked store of value that may participate in net worth summaries

The exact labels shown in the UI may be more user-friendly, but the data model must distinguish these behaviors.

### 2. Account Fields
Each account must support at minimum:

- name
- type
- opening balance
- opening balance date
- active/archived status
- optional note

Future fields such as institution name, masked account number, credit limit, or display color are optional and can be added later.

### 3. Account Lifecycle
- A user must be able to create an account.
- A user must be able to archive an account.
- Archived accounts must remain in historical reports and transaction history.
- Deleting an account that has transactions must be blocked or replaced with an archive workflow.

### 4. Opening Balance
- An account may be created with an opening balance.
- The opening balance must contribute to the running account balance from its opening date forward.
- Opening balance handling must be deterministic and should not require the user to fake a normal transaction if the product chooses to model it separately.

### 5. Balance Calculation
The system must be able to calculate the current balance of an account from:

- opening balance
- posted inflow transactions into the account
- posted outflow transactions from the account
- posted transfers in and out
- balance adjustments

Pending or future scheduled transactions must not change the current posted balance.

### 6. Transfers
A transfer is a movement of value between two accounts owned by the same user dataset.

Transfer rules:

- A transfer must specify a source account and a destination account.
- A transfer must create a balanced money movement between those accounts.
- A transfer must not count as income or expense in standard category spending reports.
- A transfer may optionally include a note.
- A transfer may optionally include a fee as a separate categorized expense line, but the fee handling can be deferred if not implemented in the initial pass.

### 7. Transfer Restrictions
- A transfer cannot use the same account as both source and destination.
- A transfer amount must be greater than zero.
- If either referenced account is archived, the system must define whether historical editing is allowed, but new transfers into a fully closed flow should be discouraged or blocked according to UX policy.

### 8. Credit Card and Liability Behavior
For v1, credit card and liability accounts must still behave as accounts with balances.
The product does not need full debt-management workflows yet, but it must support:

- recording purchases against the account
- recording payments to the account via transfer or adjustment
- showing current balance direction consistently

The UI must make it clear that a liability balance is not equivalent to available cash.

### 9. Balance Adjustments
The system must support a balance adjustment or correction workflow for situations where the tracked balance does not match reality.

Adjustment rules:

- the user must be able to set a corrected account balance
- the system must record an adjustment event or transaction instead of silently mutating history
- the adjustment must be auditable in local history

### 10. Account Reporting
The system should support at minimum:

- per-account current balance
- total cash-like balance across selected account types
- net worth style summary once asset and liability types are included in reporting

If net worth reporting is not implemented in the initial UI, the model must still allow it later without redefining account semantics.

## Data Considerations
The product model must preserve:

- account identity independent of display name
- transfer link integrity between the two sides of the movement
- ability to archive accounts without breaking history
- reproducible balance calculation from stored records

## UX Expectations
- Account creation must be simple and local-only.
- Transfers should have a dedicated entry flow instead of being disguised as a normal expense.
- Account views should show enough context for the user to understand how a balance was reached.
- The UI should not force the user to understand accounting jargon to enter common data.

## Edge Cases
- A transfer entered with the wrong date must affect historical balances when corrected.
- Archiving an account must not orphan prior transactions.
- Renaming an account must not change historical balance math.
- If an opening balance date is later than some imported transactions, the system must either prevent that state or define consistent balance ordering rules.

## Acceptance Criteria
- A user can create accounts of the supported v1 types.
- The app can calculate a current balance for an account offline.
- A transfer moves value between two accounts without appearing as category spending.
- A balance adjustment is stored as an auditable event rather than a silent overwrite.
- Archived accounts remain visible in historical data and do not corrupt prior reports.

## Follow-on Questions
- Should credit card payments always be represented as transfers?
- Should v1 expose an account detail ledger view immediately, or keep it as a later enhancement?
- Should liabilities and assets appear in the first dashboard, or only be report-ready in the data model?
