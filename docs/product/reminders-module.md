# Future Module: Reminders

## Purpose
This document defines the intended future direction for a reminders module that can live inside the same product shell, local database, and sync foundation as finance.

## Relationship To V1
Reminders are not part of the first finance-focused v1 release.
This document exists so the current architecture can preserve room for the module without inventing a separate stack later.

## Intended User Outcome
A future version should allow the user to:

- create reminder items with due dates or times
- mark reminders complete or skipped
- organize reminders by status or type
- keep reminder data available offline
- sync reminder data through the same shared sync foundation where appropriate

## Product Shape
The reminders module should:

- share the same application shell
- share the same local-first principles
- use the same stable identifier and timestamp conventions as finance entities
- avoid introducing a separate cloud dependency for v1-era architecture

## Likely Core Entities
Potential future entities include:

- reminder item
- reminder schedule
- reminder completion record
- reminder list or grouping

## Product Principles
- Reminders should work offline first.
- Reminder behavior should not require the finance module to change its core ledger semantics.
- Shared infrastructure should be reused where practical.

## Non-Goals For The First Reminder Release
- collaborative task management
- team workflows
- public cloud reminder service
- heavy productivity-suite scope
