# ADR-006: Derived Summary Strategy

- Status: Accepted
- Date: 2026-03-22

## Context

ADR-002 intentionally deferred whether finance summaries should be implemented through SQL views, cached summary tables, or both.
The product now has dashboard, budget, analytics, and account/reporting expectations that require a stable direction.

## Decision

Use a **hybrid strategy**:

- treat the transaction ledger and core business tables as the canonical source of truth
- prefer direct derivation and SQL views for correctness-first and lower-complexity summaries
- allow cached or materialized summary tables later for performance-sensitive screens where rebuild logic remains deterministic and testable

## Rationale

- The repo is still in early implementation planning and should bias toward correctness and simplicity first.
- Views or direct derivation reduce the risk of silent divergence from the canonical ledger.
- Some dashboards or analytics may eventually benefit from caching, but caching should be an optimization, not the primary truth model.

## Consequences

Positive:

- strong alignment with local-first correctness
- simpler early implementation for balances, budgets, and analytics
- safe path to later optimization if real performance pressure appears

Negative:

- very large datasets may eventually need cached summaries for smoother UX
- some advanced analytics screens may require more careful tuning later

## Rules

- Cached summaries, if introduced, must be rebuildable from source tables.
- Source ledger data remains canonical.
- Budget and analytics outputs must stay explainable from transactions and budget entries.
- Cache invalidation rules must be explicit and test-covered before shipping.
