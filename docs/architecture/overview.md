# Architecture Overview

## Summary

The application is a local-first Flutter app with a single embedded SQLite database and an optional Google Drive sync adapter for v1.
The local database is the source of truth during normal native operation.
Sync moves versioned database snapshots between devices; it does not turn Google Drive into a live primary database.

The current architecture is intentionally optimized for native local-first delivery first, while preserving seams that can support a future self-hosted web mode.
That future mode is an additional deployment topology, not a change to the v1 source of truth model.

## High-Level Components

- `App shell`: startup, routing, theming, settings entry points
- `Feature modules`: finance first, later reminders/subscriptions/assets
- `Application services`: use cases and orchestration that should stay portable across deployment modes
- `Local store`: Drift schema, migrations, repositories, transactional writes
- `Sync engine`: snapshot creation, manifest comparison, upload/download orchestration
- `Auth layer`: platform-specific Google authentication behind one interface
- `Platform services`: filesystem paths, secure storage, network reachability, device metadata

## Current Deployment Baseline

### Native local-first mode

This is the only deployment mode targeted by v1 implementation:

- Flutter app runs on the user device
- Drift and SQLite persist the active dataset locally
- Google Drive is an optional snapshot transport, not a live backend
- feature logic runs locally in the app process

### Future deployment mode

A later version may add a self-hosted browser-accessible mode:

- browser client
- application service
- server-side persistent database
- Docker-friendly deployment

That future shape must reuse stable domain and feature boundaries where practical, but it is not part of v1.

## Data Flow

### Normal app usage

1. UI calls feature application logic.
2. Feature logic reads/writes through repositories backed by interfaces.
3. The native local repository implementation persists through Drift to SQLite.
4. Derived summaries are computed locally from the same store.

### Sync upload

1. User taps sync.
2. Sync engine requests an authorized HTTP client from `AuthProvider`.
3. Sync engine asks `LocalStore` for a consistent snapshot and metadata.
4. `SyncRemote` uploads the compressed snapshot and manifest to Drive.
5. Local sync state is updated only after remote success.

### Sync download

1. User taps sync or download.
2. Sync engine fetches the remote manifest.
3. Sync engine compares remote metadata against local dirty state and last sync metadata.
4. If replacement is allowed, the snapshot is downloaded and validated.
5. `LocalStore` replaces the database safely and reopens it.

## Design Constraints

- UI code must not know whether auth came from Android Google Sign-In or Linux desktop OAuth.
- Feature modules must not call Google APIs directly.
- Feature logic should depend on repository and service contracts rather than on concrete Drift or platform APIs.
- Sync logic must never write into the live database file in place while it is open.
- Future modules must extend the same local store and sync metadata model.
- Current design choices must not assume that native local-first is the only possible deployment topology forever.

## Suggested Repository Layout For Implementation

```text
.
├── README.md
├── docs/
└── app/
    ├── pubspec.yaml
    ├── lib/
    │   ├── app/
    │   ├── bootstrap/
    │   ├── core/
    │   ├── features/
    │   │   ├── finance/
    │   │   ├── settings/
    │   │   └── sync/
    │   ├── platform/
    │   └── data/
    └── test/
```

This keeps the first implementation simple.
If the sync engine or platform adapters become reusable or hard to test in-place, extract them into local packages later.

For future deployment-mode expansion, also see:

- [`future-deployment-modes.md`](future-deployment-modes.md)
- [`../decisions/adr-004-future-web-readiness.md`](../decisions/adr-004-future-web-readiness.md)
