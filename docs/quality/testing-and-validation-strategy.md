# Testing and Validation Strategy

## Purpose
This document defines the minimum validation expectations for a local-first finance application with import/export, migration, backup/restore, and optional snapshot sync.

## Testing Principles
- correctness before optimization
- destructive operations must be test-covered
- cross-platform assumptions must be validated on both Android and Linux
- sync and migration paths must be treated as high-risk areas

## Core Test Areas

### 1. Database and Migration Tests
Test that:

- schema migrations apply cleanly from older versions
- migrated data preserves balances, categories, budgets, and recurring templates
- restore from snapshot into a newer app version triggers the right migration behavior

### 2. Ledger Correctness Tests
Test that:

- account balances match transaction sequences
- backdated edits update balances correctly
- transfers remain balanced and linked
- refunds and adjustments follow documented rules

### 3. Budget and Analytics Tests
Test that:

- budget spent totals match posted transactions
- overspending is detected correctly
- analytics exclude transfers from spending totals
- archived categories and accounts remain correct in historical reports

### 4. Recurring and Lifecycle Tests
Test that:

- recurring occurrences generate expected dates
- scheduled items do not affect live balances until posted
- skipping one occurrence does not destroy the full series
- reconciled edits trigger the intended warning/invalidation behavior

### 5. Import / Export Tests
Test that:

- CSV mapping handles baseline supported formats
- invalid rows do not corrupt existing data
- exported data can be inspected and later re-imported with expected behavior

### 6. Backup / Restore / Sync Tests
Test that:

- backup files are usable as restore sources
- destructive restore warnings occur before overwrite
- sync snapshots validate checksum and compatibility correctly
- incompatible snapshots are rejected safely
- local unsynced changes block or gate destructive replace actions

### 7. Auth and Platform Tests
Test that:

- Android and Linux auth paths both work with their intended adapters
- sign-out clears stored credentials
- local-only mode remains usable without cloud connection

## Suggested Test Layers
- unit tests for domain rules and calculations
- repository/data-layer tests for persistence and migrations
- integration tests for import/export, backup/restore, and sync flows
- smoke tests on both Android and Linux build targets

## Minimum Release Gates
Before an implementation milestone should be considered stable, verify at minimum:

- app starts with a clean database on Android and Linux
- sample finance dataset survives migration
- manual backup and restore work locally
- sync upload and download succeed for compatible snapshots
- restore/download safety warnings appear at the right times
