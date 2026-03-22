# Import Format Reference

## Purpose
This document defines the baseline file format expectations for transaction import in v1.
It complements the product requirement for import/export and migration.

## Supported Import Type In V1
V1 must support user-provided CSV files for transaction import.

## Baseline Assumption
Imported transactions are treated as `posted` transactions unless a future version explicitly supports alternate state import.

## Recommended Columns
The importer should support mapping from CSV columns into these target fields:

- `date`
- `amount`
- `direction` or signed amount interpretation
- `account`
- `category`
- `payee`
- `note`

Not every source file will include all fields.
The product should allow partial mapping where safe.

## Required Minimum Fields
For a row to import as a usable baseline transaction, the import flow should require enough information to determine:

- effective date
- amount
- target account or selected default account for the batch
- whether the entry is income or expense if sign alone is not sufficient

## Date Rules
The importer should document accepted baseline date formats such as:

- `YYYY-MM-DD`
- `DD/MM/YYYY`
- `MM/DD/YYYY`

If ambiguous formats are present, the user must be told how the file is being interpreted.
Blind ambiguous parsing is not acceptable.

## Amount Rules
Two input styles may be supported:

### Signed amount style
Example:

- `-120.50` for expense
- `2500.00` for income

### Direction plus amount style
Example:

- `direction=expense`, `amount=120.50`
- `direction=income`, `amount=2500.00`

The importer must normalize both styles into the product transaction model consistently.

## Account Mapping Rules
The import flow should allow:

- mapping a source column to account names when present
- assigning all imported rows to a selected account when the source file contains no account column

If a referenced account does not exist, the user should be able to:

- map to an existing account
- create a new account
- abort and fix mapping

## Category and Payee Mapping Rules
When category or payee values appear in the CSV:

- existing matches may be reused
- new values may be created during import if the user approves the mapping behavior
- the import preview should make this visible

## Example Minimal CSV
```csv
date,amount,payee,note
2026-03-01,-12.50,Coffee Corner,Morning coffee
2026-03-02,2500.00,Employer,Monthly salary
```

## Example Extended CSV
```csv
date,amount,direction,account,category,payee,note
2026-03-01,12.50,expense,Wallet,Food,Coffee Corner,Morning coffee
2026-03-02,2500.00,income,Main Bank,Salary,Employer,Monthly salary
```

## Validation Rules
The importer must detect and report:

- missing required columns after mapping
- unparseable dates
- unparseable amounts
- empty rows
- unsupported file encoding or malformed CSV structure where practical

## Duplicate Handling
V1 duplicate handling may remain conservative.
The product should at least:

- warn that duplicates are not fully auto-detected
- allow the user to review before final import
- leave room for later heuristics using date, amount, account, and payee

## Import Output Expectations
A successful import should result in:

- created posted transactions
- created or reused category/payee references as documented by the mapping step
- no partial corruption if some rows fail unexpectedly

## Relationship To Export
Exported CSV should be predictable enough that it can later be re-imported with minimal remapping.
That does not require export and import schemas to be identical on day one, but closer alignment is preferred.
