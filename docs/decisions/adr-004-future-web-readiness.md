# ADR-004: Future Web Readiness

- Status: Accepted
- Date: 2026-03-22

## Context

The current product direction is intentionally optimized for:

- Android and Linux first
- local-first operation
- a single embedded database per device
- optional Google Drive snapshot sync
- no custom backend in v1

That remains the correct v1 direction.

However, there is a likely future requirement to support a browser-accessible self-hosted deployment that runs on a home server and is reachable across a trusted home network or secure remote tunnel.

If the implementation couples feature logic too tightly to local SQLite files, device-local auth, or Google Drive-specific workflows, future web support will require unnecessary rework.

## Decision

Keep the current v1 architecture decisions, but adopt the following **future-readiness constraints** immediately:

1. Feature and domain logic must depend on repository/service interfaces, not directly on Drift tables, Drive APIs, or platform SDK calls.
2. Sync and backup behavior must stay behind replaceable adapters.
3. Auth responsibilities must stay behind an interface even if the first implementation only targets native platforms.
4. Current local-first native mode and a future self-hosted server mode are both valid product deployment modes.
5. Documents and implementation structure should avoid language that assumes a local embedded database is the only possible runtime topology forever.

## Deployment Modes

### Native local-first mode

Characteristics:

- Android and Linux clients
- device-local SQLite database
- optional Google Drive snapshot sync
- no always-on backend required

### Future self-hosted web mode

Characteristics:

- browser-accessible UI
- self-hosted application service
- central persistent server-side database
- Docker-friendly deployment
- optional backups/export integrations

## Required Design Seams

The implementation should preserve these seams:

- `Feature logic -> Repository interfaces`
- `Repository interfaces -> local or remote implementations`
- `Auth contract -> native auth or future web/session auth`
- `Backup/sync contract -> Drive adapter or future backup adapter`
- `Platform services -> native adapters only, outside feature logic`

## Consequences

Positive:

- reduces future rework when adding a self-hosted mode
- keeps v1 delivery focused while preserving expansion options
- makes architecture language clearer about what is a v1 choice versus a permanent truth

Negative:

- adds some abstraction earlier than the narrowest possible v1 build
- may delay a few implementation shortcuts that directly couple UI or services to local storage internals

## Explicit Non-Decisions

This ADR does **not** decide:

- the backend framework for a future self-hosted service
- whether the future web UI is Flutter web, another web client, or a hybrid approach
- the production database choice for a future server mode
- the authentication model for remote browser sessions

Those choices should be made later once native v1 is stable and real usage pressure exists.

## Rationale

The goal is not to prematurely build a backend.
The goal is to avoid painting the project into a corner.

The current architecture can remain local-first and native-first while still preserving a clean path to a future server-backed deployment mode.

## Follow-Up Documentation

This ADR is complemented by:

- `docs/product/future-web-self-hosted-mode.md`
- future updates to architecture docs describing native mode and server mode boundaries more explicitly
