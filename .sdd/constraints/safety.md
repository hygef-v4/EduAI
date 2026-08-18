# Safety & Security Constraints — EduAI

> Guardrails preventing destructive operations, data leakage, and unauthorized AI behavior.

---

## 1. Database Safety Guardrails

- **NO Destructive DDL**: Never execute `DROP TABLE`, `DROP DATABASE`, or `TRUNCATE` in migration scripts or service code.
- **NO Unbounded DELETE**: All `DELETE` statements must include an explicit `WHERE` clause scoped to specific primary keys (`id`) or parent foreign keys (`notebook_id`).
- **Parameterized Queries Only**: Never concatenate strings into SQL queries. Use Spring Data JPA repository methods or Hibernate `@Query` with named parameters (`:paramName`) to prevent SQL Injection.
- **Soft Deletes Where Applicable**: Exam submissions, question banks, and audit logs must use soft deletion (`is_deleted = true` or `deleted_at`) to preserve historical test records.

## 2. AI Execution Guardrails

- **Zero Direct Prompt Injection**: Student essay input fields must be sanitized by escaping delimiter tokens (`"""`, ````json`, `<system>`) before being embedded in LLM prompt templates.
- **Strict Temperature Capping**:
  - Quiz Generation: `temperature = 0.4` (balanced creativity with fidelity).
  - Auto-Grading: `temperature = 0.0` (strictly deterministic evaluation).
  - JSON Schema Repair: `temperature = 0.0`.
- **Mandatory Timeout Bounds**:
  - Google Gemini API calls must timeout after **10,000ms**.
  - Local Ollama calls must timeout after **15,000ms**.
- **PII Stripping Regex**:
  - Vietnamese Student ID pattern: `[A-Z]{2}[0-9]{6,8}` $\rightarrow$ replaced with `[STUDENT_ID]`.
  - Email pattern: `[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}` $\rightarrow$ replaced with `[USER_EMAIL]`.
  - Phone number pattern: `(0|\+84)[0-9]{9,10}` $\rightarrow$ replaced with `[PHONE]`.

## 3. Secret & Credential Safety

- **No Hardcoded Secrets**: Never commit `GEMINI_API_KEY`, `JWT_SECRET`, database passwords, or OAuth client secrets into source control.
- **Environment Resolution**: All secrets must be loaded via Spring environment variables or `.env` files matching `.env.example`.
- **JWT Key Length**: `JWT_SECRET` must be at least 256 bits (32 characters) for HMAC-SHA256 signing.
- **CORS Allowlist**: CORS must only accept origins from configured frontend host (`http://localhost:3000` in dev, production domain in prod). Wildcards (`*`) with credentials are strictly forbidden.
