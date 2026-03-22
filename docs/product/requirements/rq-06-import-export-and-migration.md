# RQ-06 — Import, Export, and Migration

## Status
Proposed

## Priority
P0

## Purpose
Define how users can bring data into the app, move data out of it, and preserve ownership of their records.

This requirement is especially important for a local-first product because data portability, safe backup, and onboarding from an existing tool are major trust signals.

## Scope
This requirement covers:

- manual import from user-provided files
- manual export of user data
- migration expectations from other tools
- validation and preview behavior
- failure handling

## Out of Scope
This requirement does not yet include:

- direct bank API imports
- automated background ingestion
- institution-specific parsers for v1
- cloud-hosted migration services

## Product Decision
V1 should support at least a simple, documented CSV-based import and export path.
The product must also support whole-dataset export suitable for backup, inspection, or later migration.

## Functional Requirements

### 1. Import Sources
V1 import should support user-provided files from local storage.
At minimum, the product should define support for:

- CSV transaction import

Later support may include:

- account import
- category import
- payee import
- structured full-dataset import

### 2. Import Mapping
For transaction CSV import, the product must define how common source columns map into the product model.
At minimum, the import flow should support mapping or recognition for:

- date
- amount
- direction or sign
- account
- category
- payee or merchant text
- note or memo

If some of these fields are unavailable in the source file, the flow should still allow partial import when safe.

### 3. Import Preview
Before committing imported data, the user should be able to review:

- row count
- detected or mapped columns
- validation errors
- a small preview of parsed entries

The product should avoid blind import without a user-visible confirmation step.

### 4. Import Validation
The import flow must validate at minimum:

- required date parsing
- amount parsing
- invalid or empty rows
- unsupported file shape

If some rows are invalid, the product must clearly communicate whether:

- the whole import is rejected
- only invalid rows are skipped
- the user must correct mapping before continuing

The chosen behavior must be consistent and documented.

### 5. Duplicate Handling
V1 does not need advanced duplicate detection, but the import workflow must define a base behavior.
Recommended minimum behavior:

- warn that duplicate detection is limited
- optionally allow the user to import anyway
- leave room for later duplicate heuristics using date, amount, account, and payee

### 6. Export Types
The app must support at least:

- transaction export to CSV
- whole-dataset export in a structured application format suitable for backup or migration

The exact structured format may be JSON, SQLite snapshot, or another documented portable representation, but it must be stable enough for restore workflows.

### 7. Portability Expectations
A user must be able to export their data without requiring cloud sync.
Export must work from the local dataset alone.
This is important for user trust and aligns with the local-first product principle.

### 8. Migration Positioning
The product should document that migration from another tool may require column mapping and cleanup in v1.
V1 does not need one-click import from every competitor, but it should not make migration impossible.

### 9. Failure Handling
If an import fails:

- existing local data must remain intact
- partial writes must be avoided or rolled back safely
- the user must receive actionable feedback about why the import failed

If an export fails:

- the user must be told whether the destination write failed or the dataset preparation failed
- no local source data should be modified

## Data Considerations
The product model and import pipeline must preserve:

- transaction identity after import
- deterministic parsing rules
- ability to retry import after correction
- compatibility with later backup and restore workflows

## UX Expectations
- Import should be explicit and user-controlled.
- The app should not pretend unsupported file formats are supported.
- The export location and output type should be clear.
- Users should not need technical knowledge to retrieve their own data.

## Edge Cases
- CSV files may use different decimal separators, date formats, or column names.
- Imported rows may refer to categories or payees that do not yet exist.
- Very large files may need batching or background parsing later, but v1 can document a practical size limit if necessary.

## Acceptance Criteria
- A user can import transactions from a CSV file after reviewing a preview.
- Invalid rows or mapping issues are clearly reported.
- Existing local data is not corrupted by a failed import.
- A user can export transactions to CSV.
- A user can export the full dataset into a structured application-owned format without using a backend.

## Follow-on Questions
- Should the whole-dataset export format be a portable JSON package, a database snapshot, or both?
- Should duplicate detection remain manual in v1 or include a lightweight heuristic pass?
- Should import for accounts, categories, and payees be part of the first milestone or follow shortly after transaction CSV import?
