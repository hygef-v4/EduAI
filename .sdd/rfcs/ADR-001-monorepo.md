# ADR-001: Monorepo Architecture

## Status: APPROVED

## Context
EduAI consists of a Spring Boot backend, a Next.js frontend, database migrations, and extensive architecture/specification documentation. The development team needs an efficient workflow for local development, synchronized PR reviews, and unified CI/CD.

## Decision
Adopt a single unified **Monorepo** structure (`EduAI/`) containing `backend/`, `frontend/`, `docs/`, `.sdd/`, and a root `docker-compose.yml`.

## Consequences
- **Positive**: Single PR can update backend API, frontend consumer, and `.sdd/shared_context.md` simultaneously. Single `.env` configuration file at root.
- **Negative**: Repo size increases over time; requires multi-project build setup in CI.
