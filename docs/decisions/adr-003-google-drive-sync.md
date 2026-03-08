# ADR-003: Google Drive Sync

- Status: Accepted
- Date: 2026-03-08

## Context

The app needs optional sync between Android and Linux without running a custom backend.
The sync target should be easy for a single user to authorize and cheap to operate.

The first version favors:

- manual sync
- simple failure handling
- minimal cloud surface area

## Decision

Use Google Drive `appDataFolder` as the v1 remote storage target.

Sync behavior in v1:

- manual only
- one logical dataset per Google account
- upload/download snapshot-based sync, not row-level remote merges
- no background sync jobs

Auth strategy:

- Android: `google_sign_in` plus `extension_google_sign_in_as_googleapis_auth`
- Linux: custom desktop OAuth adapter using Google's installed-app flow with PKCE in the system browser

Google Drive scope:

- `https://www.googleapis.com/auth/drive.appdata`

## Remote Shape

Store exactly two logical remote artifacts:

1. `manifest.json`
2. `snapshot.sqlite3.gz`

`manifest.json` fields:

- `formatVersion`
- `snapshotId`
- `createdAtUtc`
- `schemaVersion`
- `appVersion`
- `deviceId`
- `sha256`
- `compressedSizeBytes`

`snapshot.sqlite3.gz` contains a compressed temporary database snapshot created locally for sync.

## Sync Rules

Upload flow:

- verify local database is healthy
- create a consistent temporary snapshot
- compress it
- hash it
- upload snapshot
- upload manifest that points to the new snapshot
- store local sync metadata after remote write succeeds

Download flow:

- fetch manifest first
- compare remote snapshot metadata with local sync metadata
- if local unsynced changes exist, block replacement and require explicit user action
- otherwise download the snapshot, validate hash, replace local database safely, and reopen the store

Conflict rule in v1:

- do not attempt automatic multi-device merge
- prefer explicit user choice when local changes exist
- treat the remote snapshot as replace-only data

## Why appDataFolder

- The folder is private to the app within the user's Drive account.
- It is hidden from normal user file browsing.
- It uses a narrow non-sensitive Drive scope.
- It avoids asking users to manage exported files manually.

## Consequences

Positive:

- no backend cost
- easy mental model for the user
- simple sync artifact design
- architecture can later swap Google Drive for another remote adapter

Negative:

- no near-real-time sync
- conflicts are handled conservatively
- Linux requires a custom auth adapter
- future encrypted payload support will need another layer around snapshot creation

## Source Constraints

- Google recommends PKCE for installed apps.
- Loopback redirects remain the recommended option for macOS, Linux, and Windows desktop apps using the desktop client type.
- Loopback redirects are deprecated for mobile OAuth client types, which is why Android cannot reuse the Linux desktop OAuth path.
