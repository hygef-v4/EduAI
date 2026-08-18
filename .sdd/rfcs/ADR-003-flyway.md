# ADR-003: Database Migration Strategy — Flyway

## Status: APPROVED

## Context
Automatic Hibernate DDL generation (`ddl-auto: update`) is error-prone, non-deterministic, and cannot reliably create custom PostgreSQL extensions like `pgvector` or manage HNSW index creation.

## Decision
Adopt **Flyway** with version-controlled SQL migration scripts (`V1__...sql` through `V9__seed_demo_data.sql`).

## Consequences
- **Positive**: Exact, reproducible schema creation across all local, CI, and demo environments.
- **Negative**: Developers must write explicit SQL migration files for schema changes.
