# RQ-03 — Payees

## Status
Proposed

## Priority
P1

## Purpose
Define payees as a first-class concept in the finance module so transaction entry, cleanup, reporting, and later automation rules can operate on a stable merchant or counterparty identity.

The current scope and UI references imply transaction entry, but they do not yet define whether merchant information is a free-text field or a reusable entity.

## Scope
This requirement covers:

- payee as a reusable entity
- payee assignment on transactions
- payee rename and merge behavior
- payee defaults for later data entry acceleration
- basic reporting and filtering expectations

## Out of Scope
This requirement does not yet include:

- bank-import merchant normalization
- online payee lookup
- contact-management features
- multi-party shared vendor directories

## Product Decision
V1 should treat payees as a reusable entity, not only a raw text field.
A transaction may still begin with typed text, but the stored model must support stable payee identity.

## Functional Requirements

### 1. Payee Entity
A payee represents the merchant, provider, person, or counterparty associated with a transaction.

A payee record must support at minimum:

- stable identifier
- display name
- active/archived status
- optional note

### 2. Payee Assignment
- A transaction may reference zero or one payee in v1.
- The transaction entry flow should allow choosing an existing payee or creating a new one during entry.
- A free-text entry should be convertible into a reusable payee record.

### 3. Optional Transactions Without Payees
- The system must allow transactions without a payee.
- Lack of a payee must not block transaction entry.
- Reports and filters should still work when some transactions have no payee.

### 4. Payee Rename Behavior
- Renaming a payee must preserve the identity relationship for all historical transactions linked to that payee.
- Historical transactions should reflect the new display name unless the product later chooses to snapshot display text at transaction time.

### 5. Payee Merge Behavior
The system should support merging duplicate payees.

Merge rules:

- one target payee survives
- all transactions pointing to the source payee are reassigned to the target payee
- the source payee is removed or marked superseded after merge
- merge should be auditable or at least require explicit confirmation

### 6. Archived Payees
- A payee can be archived to reduce noise in selection lists.
- Archived payees must remain visible in historical transactions and reports.
- Archived payees should not be offered by default in new-entry suggestions unless the user explicitly searches for them.

### 7. Payee Defaults
The model should leave room for payee-linked defaults, even if the first UI pass only partially exposes them.
Potential defaults include:

- default category
- default note or memo template
- typical transaction direction

If defaults are implemented in v1, they must be suggestions rather than irreversible auto-edits.

### 8. Search and Filtering
The app should support filtering transaction lists by payee.
Payee search should support at least:

- prefix or substring match on display name
- reuse during transaction entry
- discovery of likely duplicates by similar names in later enhancements

### 9. Reporting
The model should allow payee-based summaries later, such as:

- total spent by payee in a date range
- most frequent payees
- recent transactions by payee

These reports do not all need to ship in the initial UI, but the product model must not prevent them.

## Data Considerations
The product model must preserve:

- stable payee identifiers independent of display name
- safe reassignment during merge
- ability to store transactions without a payee
- compatibility with future rules and import cleanup workflows

## UX Expectations
- Payee selection should accelerate entry rather than create friction.
- Users should not need to manage a separate payee screen before they can enter transactions.
- Duplicate cleanup should be possible later without corrupting history.

## Edge Cases
- Similar names such as `Uber`, `UBER`, and `Uber BV` may need later deduplication support.
- A deleted or merged payee must not leave transactions in a broken state.
- Imported transactions with messy merchant text should still be storable before normalization exists.

## Acceptance Criteria
- A user can create a reusable payee and assign it to transactions.
- Transactions may exist with or without a payee.
- Renaming a payee preserves links to historical transactions.
- Duplicate payees can be merged without losing transaction history.
- Archived payees remain visible in history but are de-emphasized for new entry.

## Follow-on Questions
- Should a transaction store both a payee reference and an original imported merchant string later?
- Should payee defaults ship in v1 or wait until transaction rules are introduced?
- Should merge history be recorded explicitly for auditability?
