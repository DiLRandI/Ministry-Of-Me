# Start Here

This document is the current high-level map of the repository documentation.
Use it as the primary reading path until older index files are refreshed.

## Recommended Reading Path

### 1. Product scope and direction
- [`product/v1-scope.md`](product/v1-scope.md)
- [`product/finance-ui-reference.md`](product/finance-ui-reference.md)
- [`product/requirements/README.md`](product/requirements/README.md)
- [`product/requirements/implementation-priority.md`](product/requirements/implementation-priority.md)
- [`product/requirements/integration-notes.md`](product/requirements/integration-notes.md)

### 2. Architecture and key decisions
- [`research/cross-platform-stack-evaluation.md`](research/cross-platform-stack-evaluation.md)
- [`decisions/adr-001-client-stack.md`](decisions/adr-001-client-stack.md)
- [`decisions/adr-002-local-storage.md`](decisions/adr-002-local-storage.md)
- [`decisions/adr-003-google-drive-sync.md`](decisions/adr-003-google-drive-sync.md)
- [`decisions/adr-004-future-web-readiness.md`](decisions/adr-004-future-web-readiness.md)
- [`decisions/adr-005-identifier-strategy.md`](decisions/adr-005-identifier-strategy.md)
- [`decisions/adr-006-derived-summary-strategy.md`](decisions/adr-006-derived-summary-strategy.md)
- [`architecture/overview.md`](architecture/overview.md)
- [`architecture/module-boundaries.md`](architecture/module-boundaries.md)
- [`architecture/finance-data-model.md`](architecture/finance-data-model.md)
- [`architecture/transaction-lifecycle.md`](architecture/transaction-lifecycle.md)
- [`architecture/sync-payload-and-compatibility.md`](architecture/sync-payload-and-compatibility.md)
- [`architecture/future-deployment-modes.md`](architecture/future-deployment-modes.md)

### 3. Platform and operational setup
- [`setup/dev-environment.md`](setup/dev-environment.md)
- [`setup/google-drive-oauth.md`](setup/google-drive-oauth.md)
- [`security/v1-security-model.md`](security/v1-security-model.md)
- [`quality/testing-and-validation-strategy.md`](quality/testing-and-validation-strategy.md)

### 4. Product workflow docs
- [`product/onboarding-and-account-connection.md`](product/onboarding-and-account-connection.md)
- [`product/conflict-resolution-and-destructive-actions.md`](product/conflict-resolution-and-destructive-actions.md)
- [`product/import-format-reference.md`](product/import-format-reference.md)

### 5. Future module direction
- [`product/reminders-module.md`](product/reminders-module.md)
- [`product/subscriptions-module.md`](product/subscriptions-module.md)
- [`product/assets-module.md`](product/assets-module.md)
- [`product/future-web-self-hosted-mode.md`](product/future-web-self-hosted-mode.md)

## Finance Requirement Set
The finance requirement set currently includes:

- `rq-01` budgeting model
- `rq-02` accounts and transfers
- `rq-03` payees
- `rq-04` reconciliation
- `rq-05` recurring transactions
- `rq-06` import/export and migration
- `rq-07` backup/restore and sync safety
- `rq-08` category model
- `rq-09` transaction model
- `rq-10` analytics and reporting
- `rq-11` search, filters, and bulk edit

## Why This File Exists
Some older index documents in the repository predate the expanded requirement and future-mode documentation.
This file provides a current entry point without changing the historical meaning of those earlier docs.
