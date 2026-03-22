# Sync Payload and Compatibility

## Purpose
This document expands ADR-003 with implementation-facing guidance on what is included in a sync snapshot, how compatibility should be judged, and how the app should react to version mismatches.

## Remote Artifacts
The remote shape remains:

1. `manifest.json`
2. `snapshot.sqlite3.gz`

## Snapshot Contents
A sync snapshot should include the full local application dataset needed to restore the user’s working state for syncable modules.
That includes at minimum:

- finance-domain tables
- schema metadata required for migration compatibility
- local records needed to preserve stable identifiers and deletion semantics

The snapshot should not depend on in-memory-only state.

## Metadata That May Remain Local-Only
Some metadata may remain device-local rather than being treated as shared business data, for example:

- ephemeral UI state
- local diagnostic caches
- platform-specific auth tokens
- device-local session hints

## Manifest Role
The manifest is the fast comparison layer used before downloading the larger snapshot.
It should allow the app to answer:

- what schema version produced this snapshot
- what app version produced it
- what device produced it
- whether the downloaded bytes match the expected hash

## Compatibility Checks
Before replacing the local database from a remote snapshot, the app should evaluate at minimum:

- format version compatibility
- schema version compatibility
- whether the current app can migrate the incoming snapshot safely
- whether local unsynced changes exist

## Recommended Compatibility Outcomes

### Compatible and safe
- download and replace after normal validation

### Compatible but requires migration
- allow restore or sync only if the current app version includes the necessary migrations
- migrate only after the snapshot is validated locally

### Incompatible
- block replacement
- tell the user why
- avoid partial replace attempts

## Version-Mismatch Examples

### Newer remote snapshot than local app understands
If the remote snapshot was created by a newer app/schema that the current app cannot migrate or understand:

- block replacement
- tell the user an app update is required

### Older remote snapshot
If the current app can safely migrate the older snapshot forward:

- allow download/restore
- run migrations after validation and before opening for normal use

## Unsynced Local Change Rule
Compatibility alone is not enough.
Even if a remote snapshot is structurally compatible, replacement must still be blocked or strongly gated when local unsynced changes exist.

## Integrity Validation
Before replacement, the app should verify:

- successful download
- decompression success
- checksum match against manifest
- readable snapshot format

## Failure Principle
If any compatibility or integrity check fails:

- keep the current local database intact
- do not leave the app in a half-replaced state
- surface enough information for the user to understand the failure class

## Relationship To Restore
Restore from a local backup or full export may reuse much of the same validation logic.
The product should keep those validation paths conceptually aligned even if the source differs.
