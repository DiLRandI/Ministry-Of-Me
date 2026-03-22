# RQ-08 — Category Model

## Status
Proposed

## Priority
P0

## Purpose
Define how categories behave in the finance module so transactions, budgets, analytics, and import mapping all rely on a stable classification model.

The current scope and requirement set already assume categories exist, but their lifecycle and semantics are not yet defined.

## Scope
This requirement covers:

- category types
- category grouping
- archive and delete behavior
- ordering and visibility
- relationship to budgets and analytics
- acceptance criteria

## Out of Scope
This requirement does not yet include:

- user-defined formulas on categories
- AI-based category suggestions
- full rule-engine automation

## Product Decision
V1 uses a reusable category model with separate income and expense categories.
Categories are user-managed, stable over time, and safe to archive without destroying history.

## Functional Requirements

### 1. Category Types
V1 must support at least:

- expense categories
- income categories

A category must belong to exactly one of those types.
Transfers are not categories.

### 2. Category Groups
The model should support an optional group or parent label so categories can be organized into sections such as:

- Housing
- Transport
- Food
- Income

If the first UI pass does not expose full nested editing, the data model should still allow grouping.

### 3. Category Fields
Each category must support at minimum:

- stable identifier
- display name
- type
- optional group or parent reference
- sort order
- active or archived status
- optional color or icon later

### 4. Budget Relationship
Only expense categories participate in the v1 monthly budget model unless a later requirement explicitly extends budgeting to income.
A category with no budget remains valid for transaction entry.

### 5. Transaction Relationship
- A normal income or expense transaction may reference zero or one category in v1.
- Transfers must not require categories.
- A future split-transaction model may allow multiple categories per transaction, but the base category identity rules must remain stable.

### 6. Archive Behavior
- A category may be archived.
- Archived categories must remain visible in historical transactions, budgets, and reports.
- Archived categories should be de-emphasized in new-entry pickers by default.

### 7. Delete Behavior
- Deleting a category with transaction or budget history must be blocked.
- If a true delete exists for unused categories, it must apply only to categories with no historical references.
- The normal cleanup path should be archive or merge, not destructive deletion.

### 8. Rename and Merge Behavior
- Renaming a category must preserve historical linkage.
- The product should support merging duplicate categories in a later enhancement.
- A merge must reassign historical references safely and preserve reports.

### 9. Ordering and Visibility
- Categories should support user-controlled ordering.
- The UI should be able to show categories grouped and sorted predictably.
- Hidden or archived categories must not silently disappear from historical views.

### 10. Default Categories
The app may provide default starter categories on first run.
If so:

- they must be editable
- they must be archivable
- they must not be treated as permanent protected system categories unless explicitly documented

## Data Considerations
The model must preserve:

- stable category identity independent of display name
- type consistency between income and expense
- safe archival without loss of history
- compatibility with budgets, reports, and later automation rules

## UX Expectations
- Category selection must be quick during transaction entry.
- Users should understand the difference between active and archived categories.
- Grouping should improve scanning without forcing complex taxonomy management.

## Edge Cases
- Imported transactions may reference categories that do not yet exist.
- A category rename must not break budget history.
- Categories used by recurring templates must remain resolvable even if later archived.

## Acceptance Criteria
- A user can create, rename, archive, and order categories.
- Categories are clearly typed as income or expense.
- Historical transactions continue to resolve the correct category after rename or archive.
- Deleting a category with history is blocked or replaced by a safe archive flow.
- Expense categories can participate in monthly budgets without special-case logic.
