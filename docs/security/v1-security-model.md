# V1 Security Model

## Purpose
This document defines the practical security posture for the first product release.
It does not over-promise strong protections that are explicitly deferred, but it makes the baseline trust model clear.

## Security Posture Summary
V1 is a local-first personal application with optional Google Drive snapshot sync.
It is designed to be privacy-conscious and low-cost, but it is **not** a full zero-knowledge or end-to-end encrypted system in the first prototype.

## What V1 Protects
V1 should protect at minimum:

- Google OAuth tokens and refresh credentials using platform-appropriate secure storage
- accidental data loss through explicit backup/restore/sync safety rules
- unsafe overwrite through destructive-action warnings
- unnecessary cloud data exposure by using Google Drive `appDataFolder` and a narrow scope

## What V1 Does Not Yet Promise
V1 does not yet guarantee:

- encrypted local database at rest
- end-to-end encrypted cloud payloads
- biometric gate or local app lock
- protection against a fully compromised device user account
- production-grade hardened secret management beyond the native-app baseline

## Local Data Reality
Because the product is local-first, the local database is the primary working dataset.
That means device security matters.
If the device itself is compromised, V1 may not be able to fully protect the user’s stored finance data.

## Cloud Sync Reality
Drive sync in v1 is intended for convenience and low-cost device-to-device transfer.
It is not the same as a zero-knowledge encrypted backup system.
Users should understand that cloud snapshots are part of the trust boundary for the linked Google account.

## OAuth Rules
- Use separate Android and desktop OAuth clients.
- Treat the desktop app as a public client.
- Use PKCE for the desktop flow.
- Store tokens in secure storage, not in plain application settings.
- Clear local auth material on sign-out.

## Backup and Restore Safety
Security for v1 includes user-safety behavior, not only cryptography.
The product must:

- warn before destructive restore or replace actions
- distinguish backup, restore, sync, sign-out, and reset terminology clearly
- avoid silent local overwrite

## Logging and Diagnostics
The app should avoid writing sensitive credential material into logs.
If diagnostics are shown to the user, they should focus on sync state, migration state, and operation outcomes rather than exposing secrets.

## Recommended Future Upgrades
After the first prototype, the next security improvements should be:

1. encrypted local database or encrypted payload support
2. encrypted backup/export options
3. app lock or biometric gate
4. stronger remote snapshot confidentiality guarantees

## Acceptance Criteria
- OAuth credentials are stored using secure storage.
- Sign-out clears stored auth/session credentials.
- Destructive data-replacement actions are clearly warned.
- The product documentation does not imply end-to-end encryption exists in v1 when it does not.
