# Future Mode: Self-Hosted Web

## Purpose

This document defines the intended future product direction for a browser-accessible deployment of `Ministry of Me`.
It does **not** change v1.
V1 remains Android + Linux first, local-first, and optional Google Drive sync.

The purpose of this document is to make sure current architecture and implementation choices do not block a later web-accessible deployment that runs inside the user's home network.

## Target User Outcome

A future version should allow a user to:

- run `Ministry of Me` on a home server or always-on machine
- deploy it with Docker or Docker Compose
- access it from a browser on phones, tablets, laptops, and desktops
- use the same central dataset from multiple devices while connected to the home network or a secure remote tunnel such as a VPN

## What This Future Mode Is

This future mode is **not** just "turn on Flutter web".

The intended shape is:

- browser-based UI
- self-hosted application service
- central persistent database on the server
- optional backup/export integrations
- network access controlled by the user

This is a different deployment mode from the native local-first mode.

## Relationship To V1

V1 assumptions that remain valid:

- finance stays the first module
- modules continue to share one conceptual product shell
- boundaries remain important between feature logic, persistence, auth, and sync/backup
- the product should still be able to operate with low or near-zero recurring cloud cost

V1 assumptions that must **not** be hard-coded in ways that block future web support:

- feature code must not assume a local embedded database is always the only runtime store
- feature code must not depend directly on Google Drive APIs
- the app must not assume that sync is always snapshot replacement between peer devices
- platform code must not leak into core domain logic

## Future Product Goals

- Provide a central home-network deployment mode for a single user first.
- Reuse as much domain logic, validation, and UI design language as practical.
- Keep the user experience simple enough for self-hosting on modest hardware.
- Preserve a path for native local-first clients to coexist with a server-backed mode.

## Non-Goals For The First Self-Hosted Release

- multi-tenant SaaS hosting
- public cloud service operated by the project
- complex team collaboration
- real-time collaborative editing
- enterprise identity integrations
- internet-exposed deployment without a reverse proxy or secure tunnel

## Recommended Functional Shape

For the first self-hosted release:

- single-user or household-admin model first
- browser UI for core finance workflows
- central database hosted on the server
- import/export and backup flows
- basic operational diagnostics for backups, migrations, and storage health

## Product Principles For Future Compatibility

- Keep domain rules independent from storage location.
- Treat deployment mode as a product capability, not as a hidden implementation accident.
- Keep backup/sync adapters replaceable.
- Prefer explicit interfaces between features and persistence.
- Preserve offline-friendly native possibilities even if a server-backed mode is added later.

## Architectural Consequence

The project should be implemented so that future deployment modes can differ in **where data lives** and **how clients access it**, without rewriting finance logic or the broader module structure.

That means:

- domain/application logic should stay portable
- repositories/services should hide storage and transport details
- auth, backup, and remote access concerns must remain behind adapters

## Suggested Roadmap Position

This mode is a **post-v1** roadmap item.

A reasonable order is:

1. complete native v1 foundations
2. stabilize data model and module boundaries
3. introduce interfaces needed for alternate deployment modes
4. design and build the self-hosted web mode

## Success Criteria For This Future Mode

- The app can run as containers on a home server.
- A browser on the same trusted network can use the application.
- Data survives container restarts via persistent volumes.
- The finance domain does not need to be rewritten to support the server-backed deployment mode.
- Future modules can use the same deployment foundation.