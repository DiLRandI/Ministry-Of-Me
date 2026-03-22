# Future Deployment Modes

## Purpose

This document extends the architecture notes with an explicit statement that `Ministry of Me` may support more than one deployment mode over time.

It does not replace the v1 architecture.
It clarifies how the current design should stay compatible with a future self-hosted web mode.

## Baseline

Current v1 baseline:

- native clients first
- local-first behavior
- embedded SQLite per device
- optional Google Drive snapshot sync

That remains the implementation target for v1.

## Deployment Modes

### Mode A: Native local-first

Topology:

- application runs on the user device
- local embedded database stores the working dataset
- optional remote backup/sync adapter exchanges snapshots

Best fit:

- Android
- Linux desktop

Characteristics:

- offline-first user experience
- no always-on backend required
- device remains the active runtime boundary

### Mode B: Future self-hosted web

Topology:

- browser client connects to an application service
- application service uses a server-side database
- deployment runs on a home server or always-on machine
- packaging should be Docker-friendly

Best fit:

- browser access from multiple devices on a trusted home network
- central always-on dataset
- household or single-user self-hosted operation

Characteristics:

- central persistence
- network dependency for active use
- server process becomes the runtime boundary instead of a single device

## Architecture Rule

The system should separate:

- domain rules
- application use cases
- persistence implementations
- remote adapters
- platform adapters

so that deployment mode changes do not force a rewrite of finance logic.

## Boundary Guidance

### Stable layers across both modes

These should remain conceptually stable:

- feature modules
- domain validation
- application services/use cases
- shared identifiers and time abstractions
- error and result contracts

### Variable layers by deployment mode

These may differ:

- persistence implementation
- auth/session implementation
- backup/sync implementation
- process topology
- transport protocol between UI and data operations

## Practical Implications For Implementation

To stay compatible with a future web mode:

- do not let widgets or screens depend directly on Drift-generated tables
- do not let feature code call Google Drive APIs directly
- keep snapshot/export logic in dedicated adapters
- treat repository interfaces as the stable boundary for feature logic
- keep platform-specific services behind dedicated adapters

## Repository-Shape Implication

The current single-repo approach remains valid.

A future implementation may evolve toward a structure like:

```text
.
├── docs/
├── app_native/
├── app_web/          # optional future browser client or shared web entry
├── services/
│   └── app_server/   # optional future self-hosted backend
└── shared/
    ├── domain/
    └── contracts/
```

This structure is only illustrative.
The important architectural point is separation of stable domain logic from deployment-specific runtime code.

## Data Model Implication

The conceptual data model should stay shared across modes even if storage changes:

- finance entities remain the same logical domain objects
- future modules should extend the same domain foundation
- timestamps, identifiers, and deletion semantics should remain portable

## Migration Principle

A future self-hosted mode should be introduced as an **additional deployment mode**, not as a forced rewrite that invalidates the native local-first path.

That means native and server-backed modes may coexist for some time, even if they eventually diverge in operational behavior.
