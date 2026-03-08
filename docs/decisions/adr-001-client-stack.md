# ADR-001: Client Stack

- Status: Accepted
- Date: 2026-03-08

## Context

The product needs:

- Android and Linux support first
- a shared codebase
- a local-first architecture
- no dependency on Apple hardware for initial development
- room to grow from finance into adjacent personal-management modules

The stack also needs to tolerate one known complication: Google authentication differs between Android and Linux desktop.

## Decision

Use:

- `Flutter` stable as the application runtime and UI framework
- `Dart 3` as the primary language
- `flutter_riverpod` for application state and dependency wiring
- `go_router` for navigation

Supporting infrastructure packages:

- `drift` for typed SQLite access
- `path_provider` for platform storage paths
- `flutter_secure_storage` for tokens and sensitive local metadata
- `googleapis` for Drive API access
- `googleapis_auth` for authorized client handling
- `google_sign_in` on Android
- `extension_google_sign_in_as_googleapis_auth` on Android

Repository strategy:

- Start as a single Flutter application repository.
- Keep features modular inside the app instead of introducing a package workspace on day one.
- Extract local packages only when interfaces are stable and there is clear reuse or test-value in the split.

## Rationale

- Flutter gives the most direct Android + Linux path with one UI framework.
- Dart keeps most of the application in one language, including data, sync, and many platform integrations.
- Riverpod fits a growing local-first app without forcing widget tree dependency injection everywhere.
- `go_router` is the standard choice for a Flutter app expected to grow beyond a few screens.
- The supporting package set covers persistence, secure storage, filesystem access, and Drive integration without introducing a backend service.

## Consequences

Positive:

- fast implementation path from the current empty repo
- one dominant language for app, domain, and most infrastructure
- strong package ecosystem for database, file paths, secure storage, and Google APIs

Negative:

- larger runtime footprint than a more native-only stack
- Linux Google auth needs a custom adapter instead of a single official sign-in package
- if the team later prioritizes Android-native-first development, Kotlin Multiplatform may look more attractive than it does today

## Rejected Alternative

`Compose Multiplatform + Kotlin + SQLDelight` was the closest alternative.
It remains a reasonable fallback if the project later moves toward a Kotlin-first team, but it is not the fastest route for this repo's current constraints.
