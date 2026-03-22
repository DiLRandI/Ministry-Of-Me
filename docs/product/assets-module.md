# Future Module: Assets

## Purpose
This document defines the intended future direction for lightweight asset tracking within the same product shell and local-first architecture as finance.

## Relationship To V1
The v1 scope already reserves room for lightweight asset management.
This document clarifies that direction without turning assets into a current implementation requirement.

## Intended User Outcome
A future version should allow the user to:

- record personal assets
- group assets by type
- capture acquisition value and current estimated value
- track valuation date
- include assets in net-worth style reporting later

## Relationship To Finance
Assets should integrate naturally with broader financial summaries, but the first asset release does not need full investment accounting.
The module should stay lightweight and focused on personal-value tracking.

## Likely Core Entities
Potential future entities include:

- asset item
- asset type
- valuation snapshot
- optional linked account reference where relevant

## Product Principles
- asset data should remain usable offline
- the module should reuse shared identifier, timestamp, and sync conventions
- the module should not force the finance ledger to become an investment platform prematurely

## Non-Goals For The First Asset Release
- brokerage integrations
- tax lot accounting
- real-time market pricing
- full portfolio analytics
