# RQ-10 — Analytics and Reporting

## Status
Proposed

## Priority
P1

## Purpose
Define what the analytics screen and related reports mean in the product so charts, summaries, and comparisons are driven by explicit behavior rather than only UI placeholders.

## Scope
This requirement covers:

- core report types
- supported dimensions and filters
- period comparison behavior
- chart and summary expectations
- acceptance criteria

## Out of Scope
This requirement does not yet include:

- tax reporting
- investment performance analytics
- AI-generated financial insights
- household or multi-user comparative analytics

## Product Decision
V1 analytics focuses on understandable personal-finance summaries derived from the same local ledger used by transactions, budgets, and accounts.
Analytics is read-only and must not redefine the ledger.

## Functional Requirements

### 1. Minimum Report Set
The analytics surface should support at minimum:

- spending by category over a selected period
- income vs expense summary over a selected period
- budget vs actual view for the current month or chosen month
- account balance trend or point-in-time balance summary where feasible

### 2. Period Selection
The user must be able to analyze at least:

- current month
- previous month
- custom month selection
- date range support in a later enhancement

### 3. Filters
The analytics model should support filters by:

- date period
- account
- category
- transaction kind where applicable

Payee and tag filters may be added later if those dimensions are available.

### 4. Budget Relationship
Analytics must remain consistent with the budget model.
If budget vs actual is shown:

- only categories and transactions that are valid for the chosen month may be included
- overspending must be visible
- categories without budgets must be handled consistently and not silently misrepresented

### 5. Transfer Treatment
Transfers must not inflate income or expense analytics.
They may appear in dedicated account movement views later, but standard spending analytics must exclude them from income/expense totals.

### 6. Refund and Adjustment Treatment
Refunds and adjustments must follow the documented transaction model.
Their treatment must be consistent across:

- category spending
- income/expense totals
- account-balance-based views

### 7. Chart Expectations
Charts should be supportive rather than the only source of truth.
Every major chart view should have accompanying numeric summary values.
Possible chart types include:

- category breakdown
- monthly trend bars or lines
- budget progress bars

### 8. Comparison Views
A comparison view is recommended for v1.1 or later, but the model should allow:

- current month vs previous month
- category spend delta by period

### 9. Offline Behavior
Analytics must work fully from local data without network access.
Cloud sync is not required to calculate reports.

## Data Considerations
The product should prefer deterministic derivation from the transaction ledger.
If cached summaries are introduced for performance, they must be rebuildable from source data.

## UX Expectations
- Analytics should be understandable to a non-expert user.
- The UI must make it clear what period and filters are active.
- Numbers displayed in analytics must be traceable back to transaction history.

## Edge Cases
- Sparse datasets should still produce sensible empty states.
- Backdated transactions should update historical reports.
- Archived categories or accounts must remain correctly represented in historical analytics.

## Acceptance Criteria
- A user can view at least one category-spending report and one income-vs-expense summary offline.
- Transfers are excluded from standard spending totals.
- Budget vs actual views match the documented budget rules.
- Active filters and periods are visible to the user.
- Analytics values can be reconciled with transaction history and account balances.
