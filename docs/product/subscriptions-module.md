# Future Module: Subscriptions

## Purpose
This document defines the intended future direction for subscription tracking as a module that builds on the same local-first product foundation as finance.

## Relationship To V1
The v1 scope already hints at subscription tracking as a later module.
This document makes that future direction explicit without expanding the current release scope.

## Intended User Outcome
A future version should allow the user to:

- track recurring subscription services
- record renewal amount and cadence
- see upcoming renewals
- detect or review payment history where finance data supports it
- receive reminders or alerts in later iterations

## Relationship To Finance
The subscription module should build on recurring transaction and payee concepts where practical.
It should not invent a totally separate schedule model if the finance recurring foundation already covers the needed semantics.

## Likely Core Entities
Potential future entities include:

- subscription definition
- renewal schedule
- linked payee
- linked recurring template
- alert preference

## Product Principles
- subscriptions should be understandable as a specialized layer over recurring financial commitments
- the module should reuse shared identifiers, timestamps, and sync conventions
- subscription tracking should remain useful offline

## Non-Goals For The First Subscription Release
- direct merchant integrations
- automatic cancellation workflows
- household shared subscription governance
- complex SaaS management beyond personal tracking
