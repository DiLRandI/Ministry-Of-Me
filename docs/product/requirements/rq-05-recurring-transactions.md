# RQ-05 — Recurring Transactions

## Status
Proposed

## Priority
P0

## Purpose
Define how the finance module handles repeating financial events such as salary, rent, subscriptions, bills, and other predictable recurring entries.

Recurring transactions are important both for planning and for reducing manual data entry.
The current roadmap mentions reminders and subscription-style future modules, but the base finance requirements do not yet define recurring transaction behavior.

## Scope
This requirement covers:

- recurring transaction templates
- schedule rules
- generation of future occurrences
- relation between scheduled and posted entries
- skipped or modified occurrences
- acceptance criteria for recurring behavior

## Out of Scope
This requirement does not yet include:

- push notifications or reminder delivery
- bank-import matching of scheduled items
- multi-user approval workflows
- variable-amount prediction using machine learning

## Product Decision
V1 should support recurring transaction templates that generate expected future entries locally.
These entries help planning and can later be posted, edited, skipped, or cancelled.

## Functional Requirements

### 1. Recurring Template
A recurring transaction template must support at minimum:

- title or description
- amount
- direction or transaction type
- source account or relevant account context
- category where applicable
- optional payee
- schedule rule
- start date
- active/inactive status

Optional support for notes is recommended.

### 2. Supported Schedule Rules
V1 should support at least:

- daily
- weekly
- monthly
- yearly

If implementation scope needs narrowing, monthly and weekly should be prioritized.
The product model should still leave room for richer rules later.

### 3. Future Occurrence Generation
The system must be able to generate expected future occurrences from a recurring template.
Generated occurrences may be stored explicitly or derived on demand, but the user-facing behavior must support:

- visibility of upcoming items
- correct date calculation
- later conversion to posted transactions

### 4. Posted vs Scheduled State
A future occurrence generated from a recurring template is not automatically a posted transaction.
The product must distinguish between:

- scheduled occurrence
- posted transaction instance

Posted transactions count toward balances and budgets according to the normal ledger rules.
Scheduled occurrences do not affect current balances until posted.

### 5. Posting Workflow
The user must be able to turn a scheduled occurrence into a real posted transaction.
During posting, the user should be able to:

- accept as-is
- edit amount
- edit date
- edit category, payee, or note if needed

### 6. Skip Behavior
The user must be able to skip a specific occurrence without deleting the entire recurring template.
Skipping one occurrence must not destroy the future schedule unless the user explicitly disables the whole series.

### 7. Edit Behavior
The product should distinguish between:

- editing one generated occurrence
- editing the recurring template for future occurrences

If the first implementation cannot support split behavior cleanly, the product must at least document the chosen limitation instead of applying edits ambiguously.

### 8. End Conditions
A recurring template may be:

- ongoing with no end date
- scheduled to end on a specific date
- limited to a fixed number of occurrences in a later enhancement

An end date is recommended in the base model even if count-based endings are deferred.

### 9. Relationship to Budgets and Reporting
- Scheduled future occurrences may be shown in planning views.
- Scheduled future occurrences must not reduce current account balances.
- Scheduled future expense occurrences should not reduce budget `spent_amount` until posted.
- Once posted, they behave like ordinary transactions in reporting.

### 10. Relationship to Subscriptions and Reminders
Recurring transactions form a foundation for future features such as:

- subscription renewals
- bill reminders
- expected salary forecasting

The finance module should implement recurring transaction behavior in a way that those later features can build on the same schedule foundation.

## Data Considerations
The product model must preserve:

- stable template identity
- predictable date generation
- separation between template definition and posted instances
- ability to skip or override a single occurrence

## UX Expectations
- Creating a recurring template should feel similar to creating a normal transaction with extra schedule fields.
- Upcoming occurrences should be easy to review.
- The user must understand which items are expected versus already posted.

## Edge Cases
- Monthly recurrences near the end of the month require a documented rule for months with fewer days.
- Leap-year annual recurrences require deterministic handling.
- Posting an occurrence late should not create duplicate posted entries accidentally.
- A user may need to change the amount for a variable bill without changing the whole series.

## Acceptance Criteria
- A user can create a recurring transaction template with a supported schedule.
- The app can show upcoming occurrences locally without network access.
- Scheduled occurrences do not affect live balances until posted.
- A user can post, skip, or disable recurring entries.
- One-off changes do not require deleting the full recurring template.

## Follow-on Questions
- Should v1 generate stored future rows or derive occurrences on demand?
- Should the first UI include a dedicated upcoming screen, or integrate upcoming items into dashboard and transaction views?
- How should end-of-month recurrence rules behave for dates such as the 29th, 30th, and 31st?
