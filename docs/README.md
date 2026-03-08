# Documentation Index

This repository is starting with architecture and decision records before application code.
The goal is to lock the v1 technology stack for an Android + Linux local-first app with Google Drive sync, then implement against those decisions without reopening the same debates.

## Reading Order

1. [`product/v1-scope.md`](product/v1-scope.md)
2. [`research/cross-platform-stack-evaluation.md`](research/cross-platform-stack-evaluation.md)
3. [`decisions/adr-001-client-stack.md`](decisions/adr-001-client-stack.md)
4. [`decisions/adr-002-local-storage.md`](decisions/adr-002-local-storage.md)
5. [`decisions/adr-003-google-drive-sync.md`](decisions/adr-003-google-drive-sync.md)
6. [`architecture/overview.md`](architecture/overview.md)
7. [`architecture/module-boundaries.md`](architecture/module-boundaries.md)
8. [`setup/dev-environment.md`](setup/dev-environment.md)
9. [`setup/google-drive-oauth.md`](setup/google-drive-oauth.md)

## ADR Status

| ADR | Title | Status | Purpose |
| --- | --- | --- | --- |
| ADR-001 | Client stack | Accepted | Choose the cross-platform runtime, language, and UI approach. |
| ADR-002 | Local storage | Accepted | Choose the database engine, access layer, and local persistence constraints. |
| ADR-003 | Google Drive sync | Accepted | Choose the v1 sync transport, sync shape, and auth strategy. |

## Folder Layout

- `docs/product/`: product intent, scope, and near-term roadmap boundaries
- `docs/research/`: comparison notes, tradeoff analysis, rejected options
- `docs/decisions/`: ADRs that lock architecture choices
- `docs/architecture/`: implementation-facing structure and interface boundaries
- `docs/setup/`: environment preparation and external service configuration

## Update Policy

- When a decision changes, add a new ADR or superseding note instead of silently editing history.
- Refresh external-package versions during implementation startup, not ad hoc in unrelated changes.
- Keep official vendor documentation links in research/setup docs so later implementation can re-verify current constraints quickly.
