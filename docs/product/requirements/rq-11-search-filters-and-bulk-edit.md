# RQ-11 — Search, Filters, and Bulk Edit

## Status
Proposed

## Priority
P1

## Purpose
Define the data-cleanup and navigation tools that make a growing finance dataset manageable over time.

## Scope
This requirement covers:

- transaction search
- filter dimensions
- saved filter potential
- bulk edit actions
- bulk review use cases

## Out of Scope
This requirement does not yet include:

- AI-generated cleanup suggestions
- advanced deduplication beyond import review
- natural-language search

## Product Decision
V1.1 or later should provide practical search and bulk-edit tools once the core ledger is stable.
The model should be prepared for these capabilities even if the first UI pass remains simple.

## Functional Requirements

### 1. Search Dimensions
The transaction list should be searchable by at least:

- note text
- payee name
- category name

### 2. Filter Dimensions
The transaction list should support filters by at least:

- date period
- account
- category
- payee
- transaction kind
- state where applicable

### 3. Bulk Actions
Bulk actions should support at minimum later on:

- assign category
- assign payee
- archive or review candidates separately where appropriate
- delete with explicit confirmation

### 4. Safety Rule
Bulk actions must be previewable and reversible where practical.
At minimum, destructive bulk operations require explicit confirmation.

### 5. Relationship To Import Cleanup
Search and bulk edit should make it easier to clean imported datasets after initial CSV onboarding.

## Acceptance Criteria
- The model supports transaction filtering across the main finance dimensions.
- Bulk category or payee reassignment can be added without redefining the data model.
- Destructive bulk actions require explicit confirmation.
