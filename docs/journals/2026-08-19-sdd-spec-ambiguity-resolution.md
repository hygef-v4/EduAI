# Journal: SDD Spec Ambiguity Resolution — Phase 2 Complete

**Date**: 2026-08-19  
**Session Type**: Spec Review & Clarification  
**Scope**: `.sdd/` — All constraints, specs, shared context, ADR  
**Files Changed**: 13 files (+53 / −41 lines)

---

## Context

Before beginning Phase 3 (Backend Scaffold), a full systematic audit of all 8 Feature SPECs was performed using the `ag-scenario` 12-dimension decomposition framework. The audit surfaced **15 ambiguities** across 4 severity tiers that would have caused implementation errors if left unresolved.

---

## Key Decisions Made

### 1. Vector Embedding Dimension: 1536 → 768
**Decision**: Standardize on `vector(768)` for pgvector.  
**Rationale**: Google `text-embedding-004` (primary) and Ollama `nomic-embed-text` (fallback) both output 768-dimensional vectors. The original 1536 spec was inherited from OpenAI ada-002 assumptions and would have caused a hard database error on first insert.  
**Files**: `BR-08`, `global.md`, `ADR-005`, `feat-document-rag/SPEC.md`

### 2. Privilege Escalation Prevention on Public Registration
**Decision**: `POST /auth/register` only accepts `STUDENT` or `TEACHER` roles. `SYSTEM_ADMIN` and `CONTENT_ADMIN` must be seeded or assigned by an existing admin.  
**Rationale**: The spec was silent on role validation, leaving an obvious privilege escalation path where anyone could self-assign admin rights.  
**Files**: `feat-auth/SPEC.md`, `shared_context.md` (API #1)

### 3. Error Taxonomy — NONE for Perfect Answers
**Decision**: Add `NONE` to `ErrorCategory` enum, stored when essay score = max_score.  
**Rationale**: The original 5-enum taxonomy had no valid value for error-free submissions. This would have caused a runtime validation error in `AutoGradingWorker` or forced a misleading error label on correct answers.  
**Files**: `BR-20`, `feat-grading/SPEC.md`

### 4. Race Condition — Draft Sync vs. Final Submit
**Decision**: `PUT /submissions/{id}/draft` rejects with `409 Conflict` if submission is not `IN_PROGRESS`. Combined with `@Version` optimistic locking on `exam_submissions` entity.  
**Rationale**: Without state guard, a delayed 10s auto-sync could overwrite a submitted exam, potentially reverting `status = SUBMITTED` back to `IN_PROGRESS`.  
**Files**: `BR-14`, `feat-exam-engine/SPEC.md`, API #22

### 5. Exam Timeout — 60-Second Network Grace Period
**Decision**: Backend deadline = `started_at + duration_minutes + 60s`.  
**Rationale**: Strict server-side timer with no grace period would reject legitimate on-time submissions from students on poor network connections (15–30s lag is common in university settings).  
**Files**: `BR-15`, `feat-exam-engine/SPEC.md`

### 6. Anti-Cheat Tab Switch — Backend-Authoritative Count
**Decision**: `tab_switch_count` is included in every `PUT /submissions/{id}/draft` payload. Backend triggers `SUBMITTED` when count > 3, not client.  
**Rationale**: Client-only tracking is trivially bypassable via DevTools. Moving authority to the server prevents cheating by intercepting/modifying the event counter.  
**Files**: `BR-16`, `feat-exam-engine/SPEC.md`, API #22, `DraftSyncDTO`

### 7. Async Grading State Machine
**Decision**: Add `GRADING_IN_PROGRESS` status between `SUBMITTED` and `GRADED`. API #24 returns `status: "PENDING"` for in-progress essay items with 3s poll recommendation.  
**Rationale**: Without this state, the result screen had no way to distinguish "not yet graded" from "grading failed silently". The frontend needed a defined signal to poll.  
**Files**: `feat-grading/SPEC.md`, `feat-exam-engine/SPEC.md`, API #23, #24

### 8. Zero-Hallucination Citation — Normalized Whitespace Matching
**Decision**: `exact_quote` verification uses normalized whitespace (`trim + collapse`) instead of byte-exact match.  
**Rationale**: LLMs may output quotes with slightly different whitespace/newlines than the source chunk. Byte-exact matching would cause excessive false rejections of valid citations.  
**Files**: `BR-10`

### 9. SSRF Protection on Web Scraper
**Decision**: Block all private IP ranges (RFC 1918: `10.x`, `172.16-31.x`, `192.168.x`), loopback (`127.0.0.1`, `localhost`), link-local (`169.254.x.x`), and non-`http/https` protocols.  
**Rationale**: Without URL validation, the scrape endpoint is a direct SSRF attack surface exposing internal services and cloud metadata endpoints.  
**Files**: `safety.md`, `feat-document-rag/SPEC.md` (FR-RAG-04)

### 10. Daily Quota — Timezone & Error Code
**Decision**: Reset at `00:00:00 GMT+7` (Asia/Ho_Chi_Minh). Exceeding quota returns `429 Too Many Requests`.  
**Rationale**: No timezone specified = ambiguous reset time. Vietnamese university users expect midnight VN time, not UTC. Missing error code meant frontend could not handle quota exhaustion gracefully.  
**Files**: `BR-26`, `feat-admin/SPEC.md`

### 11. Logout Endpoint
**Decision**: Add `POST /auth/logout` (API #34) that logs a session audit event. JWT invalidation is client-side (token removed from storage) in v1.0.  
**Rationale**: `UC008` (Logout) was listed in the spec header but had no corresponding API in the 33-endpoint catalog. Complete omission.  
**Files**: `feat-auth/SPEC.md`, `shared_context.md`

### 12. Question Topic Field for Radar Chart
**Decision**: Add `topic VARCHAR(100)` column to `question_items` and `GeneratedQuestionDTO`.  
**Rationale**: `feat-analytics` requires grouping results by topic for the Skill Radar Chart, but `question_items` had no `topic` field — making analytics aggregation impossible without a full schema change post-implementation.  
**Files**: `feat-quiz-studio/SPEC.md`, API #17, `shared_context.md`

### 13. Batch Save Question Items
**Decision**: Add `POST /question-banks/{id}/items/batch` (API #35) for atomic bulk save after AI generation preview.  
**Rationale**: The original flow was unclear — generated questions had no clear persistence path between preview (`POST /quiz/generate`) and the bank. `Promise.all` looping single-item API calls is unreliable and non-atomic.  
**Files**: `feat-quiz-studio/SPEC.md`, `shared_context.md`

### 14. Score Precision
**Decision**: `NUMERIC(5,2)` with `RoundingMode.HALF_UP`.  
**Rationale**: No precision specified = implementation-dependent rounding across services, risking score discrepancies between grading worker and result display.  
**Files**: `BR-22`

### 15. RAG Ingestion Status Polling
**Decision**: Frontend polls `GET /notebooks/{id}/documents` every 3s until no documents remain in `PROCESSING` status.  
**Rationale**: API returned `status: "PROCESSING"` but spec never defined how the client should know when vectorization completed — needed to enable quiz generation UI.  
**Files**: `feat-document-rag/SPEC.md`

---

## API Catalog Changes

| Before | After |
|--------|-------|
| 33 REST APIs | **35 REST APIs** |
| No logout endpoint | API #34 `POST /auth/logout` |
| No batch save | API #35 `POST /question-banks/{id}/items/batch` |
| `role` unvalidated in register | `role: "STUDENT"\|"TEACHER"` enforced |
| Draft sync no tab count | `tab_switch_count` in payload |
| Submit returns only `SUBMITTED` | Returns `SUBMITTED\|GRADING_IN_PROGRESS` |

---

## Impact Assessment

- **Zero breaking changes** to existing planned API contracts — only additive.
- **2 new endpoints** added (API #34, #35).
- **3 schema additions**: `question_items.topic`, `exam_submissions.version`, `exam_submissions.tab_switch_count`.
- **1 enum expansion**: `ErrorCategory.NONE`, `SubmissionStatus.GRADING_IN_PROGRESS`.
- **1 dimension correction**: `vector(1536)` → `vector(768)` — this is a **breaking** schema change that must be corrected before any Flyway migration is written.

---

## Files Modified

```
.sdd/constraints/business.md         (+8/-8)   BR-08,10,14,15,16,20,22,26
.sdd/constraints/global.md           (+1/-1)   768-dim
.sdd/constraints/safety.md           (+2/0)    SSRF guardrail
.sdd/rfcs/ADR-005-pgvector.md        (+1/-1)   768-dim context
.sdd/shared_context.md               (+8/-6)   35 APIs, updated payloads
.sdd/specs/feat-admin/SPEC.md        (+1/-1)   GMT+7 quota reset
.sdd/specs/feat-auth/SPEC.md         (+6/-4)   FR-AUTH-06, role boundary, error table
.sdd/specs/feat-document-rag/SPEC.md (+4/-4)   768-dim, SSRF, polling
.sdd/specs/feat-exam-engine/SPEC.md  (+7/-5)   tab_switch, @Version, grace period, BDD+2
.sdd/specs/feat-grading/SPEC.md      (+6/-4)   NONE taxonomy, GRADING_IN_PROGRESS, BDD+2
.sdd/specs/feat-quiz-studio/SPEC.md  (+4/-3)   topic field, FR-QUIZ-06, batch DTO
plan.md                              (+1/-1)   33→35 APIs
```

---

## Next Steps

Phase 3 is now unblocked. Backend implementation should start with:
1. Flyway V1 — `CREATE TABLE users` (role enum: `STUDENT`, `TEACHER`, `CONTENT_ADMIN`, `SYSTEM_ADMIN`)
2. Flyway V5 — `CREATE TABLE document_chunks` with `embedding vector(768)` ← critical correction
3. Flyway V8 — `CREATE TABLE exam_submissions` with `version INT`, `tab_switch_count INT`, status enum including `GRADING_IN_PROGRESS`
4. Flyway V9 — `CREATE TABLE question_items` with `topic VARCHAR(100) NOT NULL`
