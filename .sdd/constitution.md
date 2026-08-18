# EduAI Technical Constitution

> The immutable architectural law governing all development, code generation, and AI agent behavior in the EduAI project. This document supersedes all other practices when conflicts arise.

**Version**: 1.0.0 | **Ratified**: 2026-08-18 | **Last Amended**: 2026-08-18

---

## Core Principles

### I. Monorepo-First (ADR-01)

All source code, documentation, specifications, and infrastructure configuration MUST reside in a single Git repository (`EduAI/`). The repository contains three primary workspaces: `backend/` (Spring Boot), `frontend/` (Next.js), and `docs/` (specifications & architecture). Shared environment variables (`.env`) are defined once at root level. Pull requests MUST include changes to all affected workspaces atomically.

### II. Package by Layer — Backend (ADR-02)

The Spring Boot backend MUST organize code by architectural layer, not by feature module:

```
controller/ → service/ → repository/ → entity/ → dto/ → enums/
```

- `controller/`: REST endpoints only. No business logic. Delegates to `service/`.
- `service/`: All business logic. Orchestrates `repository/` calls and external API integrations.
- `repository/`: Spring Data JPA interfaces + native `pgvector` queries. No business logic.
- `entity/`: JPA `@Entity` classes mapping 1:1 to database tables. No logic beyond getters/setters.
- `dto/`: Request and Response data transfer objects. Never expose raw entities to API consumers.
- `enums/`: Domain enumerations (`UserRole`, `QuestionType`, `SourceMode`, `ErrorCategory`, etc.).

Cross-cutting concerns reside in dedicated packages: `config/`, `security/`, `ai/`, `worker/`, `exception/`.

### III. Test-First (NON-NEGOTIABLE)

- Minimum **85%** unit + integration test coverage across Controller, Service, and Repository layers.
- Test pyramid enforced: Unit Tests (fast, isolated) > Integration Tests (DB, Spring context) > E2E Tests (Playwright).
- Red-Green-Refactor cycle: write failing test → implement minimum code → refactor.
- No code merge without passing CI pipeline.

### IV. Schema-Validated AI Output (BR-11)

100% of JSON responses from LLM API calls (Gemini or Ollama) MUST be validated against pre-defined **Draft-07 JSON Schemas** before persistence. The `JsonSchemaValidator` utility in `ai/` package is the single validation gate. Invalid responses trigger a single-pass repair prompt; if repair fails, the system falls back to the alternate LLM provider via `LlmRouter`.

### V. Zero-Hallucination RAG (BR-10)

In `FROM_NOTEBOOK` source mode, every AI-generated question MUST include:
1. A valid, existing `chunk_id` referencing `document_chunks.id`.
2. An `exact_quote` string that is a verbatim substring of the referenced chunk's `content` field.

Questions failing citation verification MUST be rejected and re-generated. In `DIRECT_TOPIC_PROMPT` mode, citations reference the user's prompt text and `chunk_id` is `null`.

### VI. Human Override Authority (BR-21)

AI grading results are advisory. Teachers MUST retain absolute authority to:
- Review any AI-assigned score.
- Override marks with documented rationale.
- All overrides are logged with teacher email, timestamp, old score, new score, and reason in an immutable audit trail.

The system MUST NOT auto-lock grades without human review capability.

### VII. PII Sanitization Before External API (BR-25, SEC-01)

Before any text payload is transmitted to Google Gemini API or any third-party service:
1. Student names MUST be replaced with anonymized tokens.
2. Student IDs (mã số sinh viên) MUST be masked.
3. Email addresses MUST be stripped.
4. The `PiiSanitizer` in `security/` package is the mandatory filter.

Local Ollama calls are exempt from PII sanitization (data stays on-premise).

---

## Technology Constraints

| Constraint | Value | Rationale |
|---|---|---|
| Java Version | 21 (LTS) | Virtual threads support, modern language features |
| Spring Boot Version | 3.3.x | Latest stable with Hibernate 6, Jakarta EE |
| Node.js Version | 20 (LTS) | Required by Next.js 14 |
| TypeScript | 5.x (strict mode) | Type safety across frontend |
| PostgreSQL | 16 with `pgvector` extension | Unified relational + vector storage |
| Database Migration | Flyway (SQL-based) | Deterministic, version-controlled schema |
| Auth Mechanism | JWT Bearer Token (HMAC-SHA256, 24h expiry) | Stateless, scalable |
| AI Primary | Google Gemini 2.0 Flash | Structured JSON output, fast inference |
| AI Fallback | Local Ollama (Qwen 2.5 / Llama 3) | Offline resilience, zero-latency fallback |

---

## Quality Gates

| Gate | Trigger | Blocker? |
|---|---|---|
| `mvn clean compile` | Every backend change | Yes — merge blocked |
| `npm run build` | Every frontend change | Yes — merge blocked |
| Unit + Integration tests pass | Every PR | Yes — merge blocked |
| Flyway migration check | Backend schema changes | Yes — merge blocked |
| JSON Schema validation | AI output pipeline | Yes — data rejected |
| PII sanitizer active | External API calls | Yes — request blocked |

---

## Governance

1. This Constitution supersedes all other development practices, coding standards, and agent instructions when conflicts arise.
2. Amendments require: documented rationale, team review, and migration plan for existing code.
3. AI agents MUST read this file before generating any code for the EduAI project.
4. All PRs and code reviews MUST verify compliance with these principles.
