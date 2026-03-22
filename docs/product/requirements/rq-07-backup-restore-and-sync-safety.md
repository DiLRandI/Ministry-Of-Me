# RQ-07 — Backup, Restore, and Sync Safety

## Status
Proposed

## Priority
P0

## Purpose
Define the difference between backup, restore, and sync, and make data-safety expectations explicit for a local-first product.

The current architecture already describes manual Google Drive snapshot sync and blocking destructive download when unsynced changes exist.
This requirement expands that into a product-level safety model.

## Scope
This requirement covers:

- local backup behavior
- restore behavior
- distinction between backup and sync
- remote snapshot handling expectations
- destructive action warnings
- basic recovery expectations

## Out of Scope
This requirement does not yet include:

- end-to-end encryption in the first prototype
- near-real-time sync
- multi-device conflict-free replication
- server-hosted recovery services

## Product Decision
V1 must clearly separate three concepts:

- the live local dataset
- backup and restore operations
- manual cloud sync through Google Drive snapshots

Users must understand that sync is not a substitute for all restore flows, and restore is a deliberate recovery action.

## Functional Requirements

### 1. Local Dataset as Source of Truth
While the app is running, the local database is the source of truth.
Cloud content is not the authoritative live state.
This preserves the local-first product principle already established in the v1 scope.

### 2. Backup
A backup is a point-in-time copy of the user dataset intended for safekeeping and later recovery.

Backup requirements:

- the user must be able to create a manual local backup
- the user must be able to identify when the backup was created
- the backup format must be suitable for later restore
- creating a backup must not mutate the live dataset

### 3. Restore
A restore replaces or rebuilds the current local dataset from a chosen backup or exported application dataset.

Restore requirements:

- restore must be an explicit user action
- restore must warn clearly before overwriting current local data
- the user should be shown enough metadata to identify the backup being restored
- after restore, the app must reopen or reload against the restored dataset cleanly

### 4. Sync
Manual sync through Google Drive remains a distinct concept from backup.

Sync requirements already implied by the existing docs must continue to hold:

- the user triggers sync explicitly
- the app can upload a sync snapshot
- the app can download the latest sync snapshot
- the app shows last successful sync timestamp and basic status
- destructive download is blocked when unsynced local changes are detected

### 5. Remote Snapshot Safety
Even in v1, the product should define minimum expectations for remote snapshots.
Recommended baseline:

- uploaded snapshots should include metadata such as timestamp and source device identifier
- the product should leave room for retaining more than a single latest snapshot later
- restore and sync flows must identify what source is being used

If only one remote snapshot is kept in the first implementation, that limitation must be visible in documentation.

### 6. Unsynced Change Protection
The app must protect the user from accidental loss when local data has changed since the last sync or backup point.
At minimum:

- destructive restore operations must warn before overwrite
- cloud download that would replace local state must be blocked or require an explicit override path when unsynced local changes exist
- the UI must show a basic dirty or changed state when practical

### 7. Start Fresh / Reset Flow
The product may support a reset or start-fresh flow later, but if introduced it must be distinct from restore.
A reset operation must not be disguised as sync or ordinary cleanup.

### 8. Recovery Expectations
If the app detects a corrupted local dataset or failed restore attempt, it should provide a recovery path where possible, such as:

- reopen last valid local state
- offer restore from backup
- show why startup or restore failed

The first implementation may keep this simple, but silent data loss is unacceptable.

### 9. Relationship to Import and Export
Backup and restore are closely related to whole-dataset export and import but are not the same thing.
A user-facing full export may be usable as a restore source if the format is designed for that purpose.
The product docs should make that relationship explicit once both features are implemented.

## Data Considerations
The product model and persistence layer must preserve:

- clear metadata for backup and snapshot origin
- deterministic restore behavior
- safe overwrite boundaries
- ability to tell whether local changes are unsynced

## UX Expectations
- The user must understand whether they are backing up, syncing, or restoring.
- Destructive actions must never be ambiguous.
- The product should favor explicit warnings over silent replacement of local data.

## Edge Cases
- A user may attempt to restore an older backup over newer local data.
- The latest cloud snapshot may come from a different device.
- A failed restore must not leave the app in a half-written unusable state if it can be avoided.
- A user may expect sync to provide historical versions even if v1 only supports a latest snapshot.

## Acceptance Criteria
- A user can create a manual local backup.
- A user can explicitly restore from a chosen backup with a destructive-action warning.
- Manual Google Drive sync continues to operate as a separate workflow from local backup.
- Download or restore actions that would overwrite unsynced local changes are blocked or clearly gated.
- Backup, restore, and sync terminology are consistently separated in the product UI and docs.

## Follow-on Questions
- Should the first whole-dataset export format double as the backup format?
- Should remote snapshots retain the last N versions once the base sync path is stable?
- Should restore require app restart/reload, or can it be hot-swapped safely in the initial implementation?
