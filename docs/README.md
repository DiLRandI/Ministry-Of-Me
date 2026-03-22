# Documentation Index

This repository is starting with architecture and decision records before application code.
The goal is to lock the v1 technology stack for an Android + Linux local-first app with Google Drive sync, then implement against those decisions without reopening the same debates.
The docs now also capture the constraints needed to preserve a clean path toward a future self-hosted web mode without changing the v1 delivery target.

## Reading Order

1. [`product/v1-scope.md`](product/v1-scope.md)
2. [`product/finance-ui-reference.md`](product/finance-ui-reference.md)
3. [`product/future-web-self-hosted-mode.md`](product/future-web-self-hosted-mode.md)
4. [`research/cross-platform-stack-evaluation.md`](research/cross-platform-stack-evaluation.md)
5. [`decisions/adr-001-client-stack.md`](decisions/adr-001-client-stack.md)
6. [`decisions/adr-002-local-storage.md`](decisions/adr-002-local-storage.md)
7. [`decisions/adr-003-google-drive-sync.md`](decisions/adr-003-google-drive-sync.md)
8. [`decisions/adr-004-future-web-readiness.md`](decisions/adr-004-future-web-readiness.md)
9. [`architecture/overview.md`](architecture/overview.md)
10. [`architecture/module-boundaries.md`](architecture/module-boundaries.md)
11. [`architecture/future-deployment-modes.md`](architecture/future-deployment-modes.md)
12. [`setup/dev-environment.md`](setup/dev-environment.md)
13. [`setup/google-drive-oauth.md`](setup/google-drive-oauth.md)

## ADR Status

| ADR | Title | Status | Purpose |
| --- | --- | --- | --- |
| ADR-001 | Client stack | Accepted | Choose the cross-platform runtime, language, and UI approach. |
| ADR-002 | Local storage | Accepted | Choose the database engine, access layer, and local persistence constraints. |
| ADR-003 | Google Drive sync | Accepted | Choose the v1 sync transport, sync shape, and auth strategy. |
| ADR-004 | Future web readiness | Accepted | Preserve a clean path to a future self-hosted browser-accessible deployment mode. |

## Folder Layout

- `docs/product/`: product intent, scope, design references, and roadmap boundaries
- `docs/research/`: comparison notes, tradeoff analysis, rejected options
- `docs/decisions/`: ADRs that lock architecture choices
- `docs/architecture/`: implementation-facing structure, interfaces, and deployment-mode boundaries
- `docs/setup/`: environment preparation and external service configuration

## Update Policy

- When a decision changes, add a new ADR or superseding note instead of silently editing history.
- Keep v1 documents explicit about what is locked for native local-first delivery versus what is preserved for future deployment modes.
- Refresh external-package versions during implementation startup, not ad hoc in unrelated changes.
- Keep official vendor documentation links in research/setup docs so later implementation can re-verify current constraints quickly.
