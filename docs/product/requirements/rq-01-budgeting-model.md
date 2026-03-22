# RQ-01 — Budgeting Model

## Status
Proposed

## Priority
P0

## Purpose
Define the budgeting behavior for the finance module so the app can calculate planned spending, actual spending, and remaining balance consistently across screens and reports.

The current product documentation includes a budget screen in the UI reference, but it does not yet define the operating rules behind that screen.
This requirement closes that gap.

## Scope
This requirement covers:

- the budgeting method used in v1
- budget periods
- category budget behavior
- spent and remaining calculations
- overspending behavior
- rollover decision for v1
- acceptance criteria for UI and storage behavior

## Out of Scope
This requirement does not yet include:

- zero-based or envelope budgeting in v1
- AI-generated budget suggestions
- shared household budgeting
- multi-currency budgets
- savings-goal forecasting beyond simple category limits

## Product Decision
V1 uses a **category-based budget vs actual** model.

A user assigns a planned amount to a category for a budget period.
Transactions in that category reduce the remaining amount for the same period.
The system shows budgeted, spent, and remaining values.

This model is intentionally simpler than a full envelope system and fits the current v1 goal of a personal, local-first finance tracker.

## Functional Requirements

### 1. Budget Period
- V1 must support monthly budgets.
- A budget period is identified by year and month.
- The UI must allow the user to view the current month and past/future months.
- Future support for weekly or custom periods is deferred.

### 2. Budget Unit
- A budget is assigned per category per month.
- A category may have no budget for a given month.
- Categories without budgets still allow transactions.

### 3. Budget Values
For each budgeted category in a month, the system must store or derive:

- `budget_amount`
- `spent_amount`
- `remaining_amount`

Definitions:

- `budget_amount`: the amount the user planned for the category in that month
- `spent_amount`: the sum of expense-side transaction amounts assigned to that category in that month
- `remaining_amount`: `budget_amount - spent_amount`

### 4. Transaction Inclusion Rules
- Only posted transactions count toward `spent_amount`.
- Pending or future scheduled transactions do not reduce the budget until posted, unless a later requirement explicitly changes that behavior.
- Expense transactions increase `spent_amount`.
- Income transactions do not reduce category budget balances unless a future model explicitly supports income budgets.
- Transfer transactions must not affect category budgets unless explicitly categorized as a fee or adjustment entry.

### 5. Overspending Behavior
- Overspending is allowed.
- When `remaining_amount` is below zero, the category must be shown as overspent.
- The UI must visually distinguish overspent categories.
- Overspending does not block transaction entry.

### 6. Rollover Behavior
- V1 does not support rollover.
- Surplus and overspending do not automatically carry into the next month.
- Each new month starts with its own independent category budget values.

### 7. Category Lifecycle Behavior
- If a category is archived or hidden later, historical budgets and spending for prior months must remain intact.
- Renaming a category must preserve its historical budget association.
- Deleting a category that has budget history must be blocked or replaced with a safe archival workflow.

### 8. Recalculation Rules
The system must recalculate impacted budget values when:

- a transaction is created
- a transaction is edited
- a transaction is deleted
- a transaction changes category
- a transaction changes posting date into another month
- a transaction changes state from pending/future to posted

### 9. Budget Creation and Editing
- The user must be able to create or edit a budget amount for a category for a selected month.
- The system may support copying the previous month’s budget values into a new month.
- Bulk copy from one month to another is recommended for v1.1 if not included in the initial implementation.

### 10. Budget Reporting
The budget screen must be able to show at minimum:

- total budgeted for the month
- total spent for budgeted categories
- total remaining for budgeted categories
- per-category budget progress

## Data Considerations
The exact schema belongs in implementation docs, but the product model must support:

- month-based budget records
- category identity stability across rename operations
- deterministic recalculation from transaction records

The product should prefer derivation of `spent_amount` from transactions rather than storing denormalized totals as the primary source of truth.
Cached summaries are acceptable for performance if they can be rebuilt safely.

## UX Expectations
- The budget screen must work on mobile and desktop/tablet layouts already described in the UI reference.
- A category row should show planned amount, spent amount, and remaining amount.
- Progress visualization should make it obvious when a category is near limit or overspent.
- The user should be able to distinguish categories with no budget from categories with a zero budget.

## Edge Cases
- Transactions entered on month boundaries must use the transaction date to determine budget period.
- A late-entered transaction for a previous month must update that previous month’s budget totals.
- Refund handling is deferred until transaction direction/refund rules are fully specified, but the eventual behavior must be documented consistently with category spending math.

## Acceptance Criteria
- A user can define a budget amount for at least one category for a selected month.
- Expense transactions posted in the same month and category reduce the remaining amount.
- Editing a transaction category or date updates the correct monthly budget totals.
- Overspending is visible and does not block data entry.
- Starting a new month does not automatically carry surplus or deficit from the previous month.
- The budget screen can show accurate budgeted, spent, and remaining values without requiring network access.

## Follow-on Questions
- Should v1 support a one-click “copy last month budget” action?
- Should refunds reduce `spent_amount` directly or be modeled through separate transaction direction rules?
- Should income categories ever participate in budgeting, or remain out of scope?
