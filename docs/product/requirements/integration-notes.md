# Finance Requirements — Integration Notes

## Purpose
This document explains how the finance requirement set relates to the existing product scope, UI reference, and architecture documents.
It exists to reduce ambiguity before implementation begins.

## Relationship to Existing Documents

### `product/v1-scope.md`
The v1 scope establishes the product boundaries:

- single user
- Android and Linux first
- local-first data model
- manual Google Drive snapshot sync
- future room for reminders, subscriptions, and assets

The requirement set does not expand those product boundaries.
Instead, it makes the finance-domain behavior more explicit inside the same scope.

### `product/finance-ui-reference.md`
The UI reference already defines the screen set and visual direction for:

- dashboard
- transactions
- budget
- analytics
- add transaction
- settings

The requirement set explains what those screens must actually mean at the product-behavior level.
For example:

- `rq-01` defines how the budget screen should calculate values
- `rq-02` defines how account balances and transfers should behave
- `rq-04` defines how account confidence and reconciliation should work
- `rq-05` defines how upcoming recurring items relate to posted transactions

### ADRs and Architecture Docs
The requirement set should remain product-facing.
When implementation begins, technical decisions such as schema shape, sync payload format, caching strategy, and UI state handling should be recorded in ADRs or architecture documents.

## What Changed in Product Clarity
Before these requirement docs, the finance module was structurally present but behaviorally incomplete.
The repository already knew that it needed accounts, categories, transactions, and summaries.
The requirement set clarifies the missing behavior around:

- budget math
- account types and transfers
- payee identity
- reconciliation checkpoints
- recurring transaction lifecycles
- import and export boundaries
- backup versus restore versus sync safety

## Non-Expansion Statement
These requirements do **not** change the following existing v1 non-goals:

- no multi-user or family sharing
- no always-on backend service
- no background sync requirement
- no iOS or macOS support in v1
- no mandatory end-to-end encryption in the first prototype

## Recommended Next Documentation Moves
Once implementation planning starts, the next useful repo additions are:

1. a finance data model document that maps product entities to tables and relationships
2. a transaction lifecycle document covering pending, posted, cleared, and reconciled states
3. an import format reference with CSV mapping examples
4. a sync payload note explaining what parts of the dataset are serialized into snapshots

## Recommended Implementation Sequence
A practical sequence is:

1. accounts and transaction ledger semantics
2. import/export plus backup/restore safety
3. budgeting and recurring logic
4. reconciliation
5. payee cleanup and data-quality improvements

This order reduces the chance of reworking core balance math later.
