# ADR-002: Local Storage

- Status: Accepted
- Date: 2026-03-08

## Context

The app is local-first.
Finance data must work offline and remain available regardless of sync state.
The same persistence approach must also support future modules such as reminders, subscriptions, and assets.

The storage layer therefore needs:

- an embedded database on Android and Linux
- transactional safety
- schema migrations
- portable data copy/export for sync
- enough structure to keep a growing schema maintainable

## Decision

Use:

- `SQLite` as the database engine
- `Drift` as the typed persistence and migration layer
- `path_provider` to resolve per-platform application data directories

Persist the main database in the platform application-support directory, not in a user-selected file path during v1.

## Storage Rules

- One application database per user profile.
- All syncable modules share the same database.
- Every syncable top-level entity must use a stable text identifier.
- Every syncable row must carry UTC timestamps for creation and last update.
- Soft deletion support must exist for syncable entities by reserving a nullable deleted timestamp.
- Schema changes must go through Drift migrations tracked in source control.

## Snapshot Rules

Sync must never upload the live open database file directly.

Instead:

- create a consistent temporary snapshot copy
- generate metadata for that snapshot
- upload the snapshot artifact, not the actively open file

This protects the app from partial uploads and reduces coupling between normal local writes and sync transport.

## Why SQLite + Drift

- SQLite is a proven embedded database with strong transactional behavior.
- Drift provides typed queries, migrations, and good Android + Linux support in Flutter.
- The combination is easier to implement and reason about than introducing a server or a custom storage engine.

## Consequences

Positive:

- stable local-first foundation
- one database for current and future modules
- explicit migrations
- easier sync export/import story than object-store-only databases

Negative:

- sync logic must respect relational schema changes over time
- raw database-level syncing is unsafe, so export/import handling still needs care
- database encryption is not included in the first implementation

## Deferred Decisions

Not locked yet:

- whether IDs are UUIDv7, ULID, or another sortable unique text format
- whether finance read models use SQL views, cached summary tables, or both
- when to add encrypted SQLite payloads
