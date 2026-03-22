# V1 Scope

## Product Summary

`Ministry of Me` starts as a personal finance tracker for a single user, built for Android and Linux first.
It is local-first, works from a local database, and optionally syncs the user's data through Google Drive so the same dataset can be used on both platforms.

The architecture must leave room for future modules such as:

- reminders
- subscription tracking
- lightweight asset management

Those future modules must share the same application shell, database, and sync foundation instead of introducing separate storage stacks.

## V1 Goals

- Deliver one shared codebase for Android and Linux.
- Keep all core data usable offline.
- Store all app data in a local embedded database.
- Support manual user-triggered sync through Google Drive.
- Keep operating cost near zero by avoiding a custom backend in v1.
- Establish module boundaries that can absorb new domains later.

## V1 Capabilities

- Track finance entities in a local database.
  The exact finance feature set can stay minimal during the first implementation pass, but the storage model must support accounts, categories, transactions, and summaries.
- Persist data safely across app restarts on both platforms.
- Allow a user to connect a Google account for sync.
- Upload a sync snapshot to Google Drive.
- Download the latest sync snapshot from Google Drive.
- Show the last successful sync timestamp, source device, and basic sync status.
- Block destructive download when local unsynced changes are detected.

## V1 Non-Goals

- iOS or macOS support
- shared family or multi-user data
- background sync or near-real-time sync
- direct collaboration between devices
- full reminder/subscription/asset implementations
- custom backend service
- end-to-end encryption in the first prototype release

## Product Principles

- Local first: the local database is the source of truth while the app is running.
- Explicit sync: users control when sync happens in v1.
- Low operational burden: no always-on server in the first release.
- Extensible schema: new modules add tables and syncable records to the same foundation.
- Reversible choices: platform adapters and sync adapters stay behind interfaces so the project can change auth or cloud transport later.

## UI Reference

The current product UI reference lives in [`finance-ui-reference.md`](finance-ui-reference.md).
That document links the Figma design file and summarizes the agreed screen set, component library, responsive behavior, dark mode palette, and Flutter implementation direction for the finance module.

## Future Compatibility Note

V1 does **not** include a browser-accessible deployment or a custom backend.
However, implementation choices made during v1 should preserve a clean path to a future self-hosted web mode.
That future direction is described in [`future-web-self-hosted-mode.md`](future-web-self-hosted-mode.md) and the related architecture/ADR documents.

## Success Criteria

- The same user dataset can be created on Android and later used on Linux after manual sync.
- A broken or unavailable network connection does not prevent normal local app use.
- The project can add a new domain module without replacing the database or sync approach.
- Development can continue from Linux hardware alone.
- The first implementation does not unnecessarily couple finance logic to native-only storage or platform APIs.

## Roadmap Themes After V1

- stronger conflict handling than "manual review or replace"
- encrypted local data and encrypted cloud payloads
- reminders and recurring events
- subscription renewals and alerts
- asset inventory and valuation snapshots
- self-hosted browser-accessible deployment mode
