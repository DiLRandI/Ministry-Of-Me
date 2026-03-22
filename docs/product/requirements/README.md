# Finance Requirements

This folder expands the product-level finance requirements that are only briefly implied in the current v1 scope.
The goal is to make ledger behavior explicit before implementation starts so schema, sync, and UI work are grounded in documented domain rules.

These requirement documents are intentionally product-facing.
They define expected behavior, constraints, and acceptance criteria without locking implementation details that belong in ADRs or architecture docs.

## Reading Order

1. [`rq-01-budgeting-model.md`](rq-01-budgeting-model.md)
2. [`rq-02-accounts-and-transfers.md`](rq-02-accounts-and-transfers.md)
3. [`rq-03-payees.md`](rq-03-payees.md)
4. [`rq-04-reconciliation.md`](rq-04-reconciliation.md)
5. [`rq-05-recurring-transactions.md`](rq-05-recurring-transactions.md)
6. [`rq-06-import-export-and-migration.md`](rq-06-import-export-and-migration.md)
7. [`rq-07-backup-restore-and-sync-safety.md`](rq-07-backup-restore-and-sync-safety.md)

## Requirement Status Model

- `Proposed`: documented and available for design review
- `Accepted`: locked for current implementation planning
- `Deferred`: intentionally postponed to a later milestone
- `Superseded`: replaced by a newer requirement document or ADR

## Notes

- These documents refine the finance module only.
- They do not expand v1 into a multi-user or server-backed product.
- When implementation discovers a technical tradeoff, record the technical choice in ADRs and keep the user-facing behavior here.
