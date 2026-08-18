# Project Execution Plan & Tracker — EduAI

> Real-time progress tracker across all implementation phases of the EduAI Platform.

---

## Phase 1: SDD Foundation & Constitution (`.sdd/`)
- [x] Create `.sdd/` directory skeleton (`constraints/`, `specs/`, `rfcs/`, `skills/`, `reviews/`)
- [x] Write `.sdd/constitution.md` (7 immutable architectural principles)
- [x] Write `.sdd/constraints/global.md` (tech stack, naming conventions, standards)
- [x] Write `.sdd/constraints/business.md` (BR-01 through BR-26 rules)
- [x] Write `.sdd/constraints/safety.md` (no destructive DDL, prompt safety, PII regex)
- [x] Write `.sdd/shared_context.md` (33 REST API contract definitions)
- [x] Write Architectural Decision Records (ADR-001 through ADR-007)
- [x] Write Domain Skills (`rag-optimization.md`, `error-diagnostics.md`)
- [x] Create `AGENTS.md` and `.agents/.agentignore`

## Phase 2: Feature SPEC Decomposition (`.sdd/specs/`)
- [x] `feat-auth` (UC001-UC008: Register, Login, JWT, Profile)
- [x] `feat-workspace` (UC004-UC006: Create Workspace, Join Code, Members)
- [x] `feat-document-rag` (UC009-UC015: File Ingestion, Prompt Ingestion, Web Scrape, pgvector)
- [x] `feat-quiz-studio` (UC016-UC029: Dual-mode Gen, Grounded Citation, Question Bank)
- [x] `feat-exam-engine` (UC030-UC051: Exam Builder, Timed Room, 10s Auto-Save, Anti-Cheat)
- [x] `feat-grading` (UC052-UC062: MCQ Instant, LLM Essay Grading, 5-Error Taxonomy, Teacher Override)
- [x] `feat-analytics` (UC063-UC073: Skill Radar, Mistake Matrix, Class Histogram)
- [x] `feat-admin` (UC074-UC082: LlmRouter Switching, Token Cost Tracker, Quotas)

---

## Next Steps: Code Implementation
- [ ] **Phase 3**: Backend Scaffold (`backend/` — Spring Boot 3.3, Flyway V1-V9, 16 Entities, Enums, Security)
- [ ] **Phase 4**: Frontend Scaffold (`frontend/` — Next.js 14, TailwindCSS, Route Groups, Types)
- [ ] **Phase 5**: Docker Compose & CI Integration (`docker-compose.yml`, `.github/workflows/ci.yml`)
