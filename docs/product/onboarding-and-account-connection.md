# Onboarding and Account Connection

## Purpose
This document defines the product behavior for first-run setup, local-only usage, Google account connection, sign-out, and sync-related settings entry points.

## Product Principle
The app is usable as a local-only finance tracker even if the user never connects Google Drive sync.
Sync is optional in v1.

## First-Run Experience
On first launch, the app should guide the user through:

1. understanding that data is stored locally first
2. creating or accepting initial finance setup defaults
3. optionally connecting a Google account for sync later

The first-run flow should not force cloud connection before the user can use the app.

## Minimum First-Run Decisions
The user should be able to:

- start with local-only mode
- continue to the app without Google sign-in
- connect Google later from settings or sync entry points

## Optional First-Run Starter Setup
The app may optionally offer:

- starter categories
- starter account creation
- currency selection if currency support remains single-currency in v1

These steps should stay lightweight.

## Google Account Connection
The product should allow the user to connect a Google account from settings or sync UI.
Connection behavior should include:

- clear statement that Drive app data is used for private sync storage
- indication that the user may continue without connecting
- visible success/failure state after sign-in

## After Successful Connection
The UI should show at minimum:

- connected Google account identity or email indicator where appropriate
- last successful sync timestamp once sync has occurred
- current sync status or last-known result

## Sign-Out Behavior
Signing out of Google in v1 should:

- clear local auth/session credentials
- keep local finance data intact by default
- disable further Drive sync until reconnection

The product should clearly distinguish between:

- disconnecting cloud sync
- deleting local data
- deleting remote snapshot data if such a feature is added later

## Account Switching
If the user connects a different Google account later, the app should treat that as a meaningful dataset/sync boundary.
The product should not silently mix one local dataset with another account’s remote snapshot without explicit warning.

## Settings Expectations
Settings should provide at minimum:

- sync/account connection entry point
- current connection status
- manual sync actions
- diagnostics or last error summary where practical
- backup/restore entry points once those features are available

## Failure States
The onboarding and account connection flows should handle at minimum:

- user declines sign-in
- OAuth flow fails or is cancelled
- Drive is unavailable
- app remains fully usable in local-only mode after cloud errors

## Acceptance Criteria
- A user can start using the app without connecting Google.
- A user can connect Google later from inside the app.
- Signing out removes sync credentials but does not wipe local data.
- The UI distinguishes local-only usage from connected sync usage.
- The product makes it clear that sync is optional and explicit in v1.
