# ADR-002: Backend Code Organization — Package by Layer

## Status: APPROVED

## Context
The team evaluated two primary backend packaging models: *Package by Feature* vs *Package by Layer*. The project has 16 interrelated database entities and 33 REST endpoints.

## Decision
Adopt **Package by Layer** (`controller/`, `service/`, `repository/`, `entity/`, `dto/`, `enums/`, `ai/`, `worker/`, `security/`, `exception/`).

## Consequences
- **Positive**: Direct mapping to Spring Boot architecture standards, clean layer boundaries, easy for team members to navigate and write unit tests for specific layers.
- **Negative**: Feature changes touch multiple directories (managed via `.sdd/specs/feat-*/TASKS.md`).
