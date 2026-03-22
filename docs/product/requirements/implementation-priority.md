# Finance Requirements — Implementation Priority

## Purpose
This document turns the finance requirement set into a practical implementation order.
It is meant to help planning without changing the product scope or architecture decisions already documented elsewhere.

## Priority Bands

### P0 — Core Finance Foundation
These requirements define the minimum behavior needed for a trustworthy personal finance product.
They should be resolved before or during the first serious implementation pass because they influence schema design, balance math, sync behavior, and UI workflows.

1. [`rq-01-budgeting-model.md`](rq-01-budgeting-model.md)
2. [`rq-02-accounts-and-transfers.md`](rq-02-accounts-and-transfers.md)
3. [`rq-04-reconciliation.md`](rq-04-reconciliation.md)
4. [`rq-05-recurring-transactions.md`](rq-05-recurring-transactions.md)
5. [`rq-06-import-export-and-migration.md`](rq-06-import-export-and-migration.md)
6. [`rq-07-backup-restore-and-sync-safety.md`](rq-07-backup-restore-and-sync-safety.md)

Why these are P0:

- they define ledger truth and balance correctness
- they reduce risk of schema rework later
- they determine how safe the product feels to use with real data
- they shape critical user trust flows such as restore, import, and reconciliation

### P1 — Entry Quality and Reporting Quality
These requirements improve data quality, long-term usability, and future automation readiness.

1. [`rq-03-payees.md`](rq-03-payees.md)

Why this is P1:

- payees greatly improve transaction quality and later automation
- the product can still function without advanced payee workflows on day one
- payee identity becomes more valuable once import and rules expand

## Suggested Release Cuts

### Release Cut A — Ledger Foundation
Goal: produce a usable offline finance tracker with safe local data handling.

Recommended inclusion:

- accounts and transfers
- import/export baseline
- backup/restore/sync safety
- minimal transaction entry and summaries already implied in scope

This cut prioritizes trustworthy storage and account balance behavior before deeper planning workflows.

### Release Cut B — Planning and Confidence
Goal: make the product meaningfully useful for monthly financial management.

Recommended inclusion:

- budgeting model
- recurring transactions
- reconciliation

This cut turns the app from a raw transaction tracker into a more complete personal finance system.

### Release Cut C — Data Quality and Cleanup
Goal: make the dataset easier to maintain over time.

Recommended inclusion:

- payees
- duplicate cleanup improvements during import
- search, filters, and automation rules in later follow-up requirements

## Dependency Notes

### Strong dependencies
- reconciliation depends on solid account semantics
- recurring transactions depend on transaction and account foundations
- import/export depends on the finance core schema being reasonably stable
- backup/restore safety depends on clear dataset ownership rules

### Soft dependencies
- payees can begin early but become more powerful once import exists
- budgeting becomes more valuable once recurring transactions are available

## Planning Recommendation
Lock these product decisions earliest:

1. account model
2. transfer behavior
3. budget calculation rules
4. recurring schedule semantics
5. restore versus sync boundaries

Those decisions have the highest chance of forcing downstream rework if postponed.

## Exit Criteria for “finance core defined”
The finance core should be considered sufficiently defined for implementation planning when:

- account balances can be explained deterministically
- transactions have clear lifecycle states
- budget math is documented
- recurring events have documented posting rules
- the user can import, export, back up, restore, and sync without ambiguity
