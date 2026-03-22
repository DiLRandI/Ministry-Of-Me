# Module Boundaries

## Goal

Keep the first implementation simple while still isolating the parts most likely to change:

- auth
- sync transport
- database adapter
- future feature modules
- future deployment mode

## App-Level Boundaries

### `app/`

- application shell
- routes
- theme
- top-level providers
- startup sequence

### `core/`

- time abstraction
- ID generation
- app errors
- logging contracts
- result types
- shared constants

### `features/finance/`

- accounts, categories, transactions, summaries
- finance-specific application services
- finance screens and view models
- domain rules that should remain valid across deployment modes

### `features/sync/`

- sync status UI
- manual sync actions
- sync orchestration use cases
- conflict prompts

### `features/settings/`

- Google account connection
- sync preferences
- diagnostics
- deployment-mode diagnostics later if needed

### `data/local/`

- Drift database
- table definitions
- migrations
- repository implementations for native local-first mode
- snapshot export/import implementation

### `platform/google_auth/`

- Android Google Sign-In adapter
- Linux desktop OAuth adapter
- secure token storage

### `data/sync/`

- Drive API integration
- manifest serialization
- snapshot upload/download transport

### `data/remote/`

Reserved for a future self-hosted mode if introduced later:

- remote repository implementations
- HTTP transport adapters
- server-backed data access contracts

## Required Internal Interfaces

These interfaces are part of the design contract and should exist before feature code spreads across the app.

### `LocalStore`

Responsibilities:

- open and close the database
- run migrations
- expose transactions
- create a sync snapshot
- inspect local dirty state
- replace the local database from a validated snapshot
- persist local sync metadata

Suggested methods:

```text
open()
close()
runInTransaction(...)
createSyncSnapshot() -> LocalSnapshot
getSyncState() -> LocalSyncState
replaceFromSnapshot(...)
markSyncCompleted(...)
```

### `FeatureRepository`

Feature-facing repository contracts should hide whether data comes from:

- native local SQLite storage
- future remote service calls
- a mixed synchronization workflow

Feature code should depend on these contracts, not on concrete Drift table access.

### `AuthProvider`

Responsibilities:

- sign in
- restore an existing session
- return an authorized HTTP client for Google APIs or future remote calls where appropriate
- sign out

Suggested methods:

```text
restoreSession() -> AuthSession?
signIn() -> AuthSession
getAuthorizedClient() -> AuthenticatedClient
signOut()
```

### `SyncRemote`

Responsibilities:

- fetch the latest manifest
- upload a manifest and snapshot
- download the current remote snapshot
- delete remote data if a reset feature is later added

Suggested methods:

```text
fetchLatestManifest() -> SyncManifest?
uploadSnapshot(...)
downloadSnapshot(...)
```

### `SyncManifest`

Required fields:

```text
formatVersion
snapshotId
createdAtUtc
schemaVersion
appVersion
deviceId
sha256
compressedSizeBytes
```

## Rules For Future Modules

- New modules add tables to the shared database unless there is a strong reason not to.
- New modules must become sync-aware by extending `LocalStore` and `SyncManifest` conventions instead of inventing new cloud storage.
- Platform-specific code must stay behind `platform/` or adapter implementations, never inside feature screens or repositories.
- Application services and repository contracts should be written so a future self-hosted mode can add alternate data implementations without rewriting feature logic.

## Cross-Reference

For broader deployment-mode guidance, also see:

- [`future-deployment-modes.md`](future-deployment-modes.md)
- [`../decisions/adr-004-future-web-readiness.md`](../decisions/adr-004-future-web-readiness.md)
