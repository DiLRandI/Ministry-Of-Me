# ADR-005: Identifier Strategy

- Status: Accepted
- Date: 2026-03-22

## Context

ADR-002 intentionally deferred the identifier format for syncable entities.
The project now has enough product detail around sync, recurring items, budgets, and future modules that the identifier strategy should be locked before implementation spreads.

The chosen ID format should:

- work well as a stable text identifier across syncable rows
- be easy to generate locally on Android and Linux
- avoid coordination with a backend service
- sort reasonably by creation time when useful for debugging and data inspection

## Decision

Use **UUIDv7**-style text identifiers for syncable top-level entities and other major durable records unless a more specific local-only record type has a clear reason not to.

## Rationale

- UUIDv7 preserves global uniqueness without a backend coordinator.
- It has time-ordered characteristics that are useful for local-first and sync-oriented systems.
- It fits the existing decision that syncable rows should use stable text identifiers.
- It avoids binding the project to integer primary keys that are awkward across export/import and device-generated data.

## Consequences

Positive:

- stable portable identifiers across devices
- good fit for future sync and backup portability
- predictable enough ordering for diagnostics and inspection

Negative:

- text identifiers are larger than compact local integers
- some internal tables may still choose a different strategy if they are truly local-only and non-syncable

## Notes

- This ADR does not require every internal helper table to expose UUIDv7 to the user.
- If Drift or local schema implementation uses surrogate row keys for performance in some non-syncable cases, the product-visible stable ID must still remain intact.
