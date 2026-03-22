# Cross-Platform Stack Evaluation

Research date: 2026-03-08

## Decision Drivers

The first stack choice must satisfy these constraints:

- one codebase for Android and Linux
- reliable local embedded database
- optional Google Drive sync without running a custom backend
- practical implementation from Linux hardware only
- room to grow into more personal-management features later
- fast delivery over "most native" purity

## Shortlisted Options

| Option | Android + Linux coverage | Local database story | Google auth/sync fit | Delivery speed | Overall fit |
| --- | --- | --- | --- | --- | --- |
| Flutter + Dart + Drift + SQLite | Strong | Strong | Strong on Android, custom desktop auth adapter required on Linux | Strong | Best fit |
| Kotlin Multiplatform + Compose + SQLDelight | Strong | Strong | Viable, but more platform wiring and Kotlin multiplatform complexity | Medium | Good fit, slower |
| Tauri v2 + Rust core + web UI | Possible | Strong | Possible, but auth + desktop/mobile split adds complexity early | Medium-Low | Not preferred for v1 |
| Avalonia + .NET + SQLite | Possible | Strong | Possible, but weaker fit for Android-first delivery and ecosystem mix | Low-Medium | Not preferred for v1 |

## Findings

### Flutter

Why it fits:

- Flutter officially supports Android and Linux desktop deployment from one framework.
- The Dart ecosystem has mature packages for local embedded persistence, file paths, secure storage, routing, and Google API access.
- Drift gives typed SQLite access with migrations and works across mobile and desktop.
- Flutter keeps UI, state, and platform integrations in one language for most of the app.

Main caveat:

- `google_sign_in` does not support Linux, so Linux needs a browser-based desktop OAuth adapter using the Google desktop-app flow with PKCE.

### Kotlin Multiplatform + Compose

Why it remained a serious alternative:

- Compose Multiplatform now supports Android and Linux.
- SQLDelight gives a strong multiplatform SQLite story with explicit drivers.
- Kotlin is attractive if the team wants deeper Android-native alignment.

Why it lost for v1:

- More build tooling and platform-specific setup to reach the same Android + Linux result.
- Desktop auth and Google API integration still need explicit platform handling.
- No existing Kotlin preference offsets that extra setup cost.

### Tauri

Why it was not selected:

- It is attractive for desktop, but the Android + Linux combination would still force a hybrid architecture earlier than needed.
- The product is data-entry heavy and local-first; a webview-first stack adds little advantage at this stage.
- It would introduce Rust plus a web UI stack without a clear payoff for the first milestone.

### Avalonia

Why it was not selected:

- It is credible for desktop and mobile, but it is not the shortest path for Android + Linux from this repo's starting point.
- It introduces a .NET-centered toolchain while the strongest supporting examples for Google Drive sync in this problem space are easier to assemble in Flutter/Dart.

## Recommendation

Choose:

- `Flutter` for UI/runtime
- `Dart` as the primary language
- `SQLite` as the embedded database engine
- `Drift` as the database access layer
- `Google Drive appDataFolder` as the v1 sync target

This is the lowest-friction stack that still leaves clean escape hatches for future growth.

## Decision Notes

- The stack choice is optimized for fast delivery, not maximum native feel.
- Google Drive sync is feasible without a backend, but Linux and Android cannot share the exact same Google auth package path.
- The right architecture response is not changing stacks; it is isolating auth behind an interface.
- A future self-hosted web mode does not automatically require changing the native-first stack choice; it requires preserving clear domain, repository, and deployment seams.

## Forward-Looking Note

This document selects the best v1 stack for native local-first delivery.
It does **not** claim that Flutter + Drift + SQLite is automatically the full answer for a future browser-accessible self-hosted mode.
The future-web requirement should be handled by architecture boundaries and later deployment decisions, not by prematurely changing the v1 stack decision.

See also:

- [`../product/future-web-self-hosted-mode.md`](../product/future-web-self-hosted-mode.md)
- [`../decisions/adr-004-future-web-readiness.md`](../decisions/adr-004-future-web-readiness.md)
- [`../architecture/future-deployment-modes.md`](../architecture/future-deployment-modes.md)

## Sources

- Flutter supported platforms: <https://docs.flutter.dev/reference/supported-platforms>
- Flutter desktop docs: <https://docs.flutter.dev/platform-integration/desktop>
- Drift platform support: <https://drift.simonbinder.eu/platforms/>
- Compose Multiplatform compatibility and supported platforms: <https://kotlinlang.org/docs/multiplatform/compose-compatibility-and-versioning.html>
- SQLDelight multiplatform SQLite docs: <https://sqldelight.github.io/sqldelight/2.1.0/multiplatform_sqlite/>
- Google OAuth for installed apps: <https://developers.google.com/identity/protocols/oauth2/native-app>
- Google Drive appDataFolder guide: <https://developers.google.com/workspace/drive/api/guides/appdata>
