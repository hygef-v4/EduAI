# Feature Specification: AI Multi-Tier Grading & Diagnostics (`feat-grading`)

> **Feature Module**: `feat-grading`  
> **Status**: `Approved`  
> **Use Cases Covered**: `UC050` (Instant Grade), `UC051` (Diagnostic Explanation), `UC056` (Error Classification), `UC059` (Teacher Override)  
> **API Endpoints**: #24 (`GET /submissions/{id}/result`), #25 (`POST /grading/{result_id}/override`)

---

## 1. Context & Goal
- **Problem**: Objective items need instant feedback; subjective essays require deep semantic scoring with actionable error classification rather than generic grades.
- **Goal**: Deterministic auto-grading for MCQs, LLM semantic evaluation for essays, classification into 5 standardized error categories, and teacher override authority.

## 2. Functional Requirements
- **FR-GRADE-01**: Exact-match auto-grading runs immediately upon exam submission for MCQ and True/False items (`BR-22`).
- **FR-GRADE-02**: `AutoGradingWorker` asynchronously grades short essay answers using Gemini 2.0 Flash against stored rubrics and reference chunks.
- **FR-GRADE-03**: AI grading classifies essay errors into exact 5 categories: `CONCEPTUAL_MISUNDERSTANDING`, `CALCULATION_ERROR`, `MISREAD_QUESTION`, `SYNTAX_ERROR`, `INCOMPLETE_LOGIC` (`BR-20`).
- **FR-GRADE-04**: Teachers can override any AI-assigned score with documented reason and audit logging (`BR-21`, API #25).
- **FR-GRADE-05**: Student result screen renders scores, item breakdown, AI reasoning, and source citations (`UC051`).

## 3. Data Model & Entities
- **Tables**: `ai_grading_results` (`score_awarded`, `max_score`, `feedback_text`, `error_category`, `is_overridden`, `overridden_by`, `override_reason`), `mistake_logs`.
- **DTOs**: `SubmissionResultDTO`, `GradingResultDTO`, `TeacherOverrideDTO`.

## 4. Error Handling
| Scenario | Expected Behavior | HTTP Status |
|---|---|---|
| Non-teacher attempting override | "Access Denied" | `403 Forbidden` |
| AI grading timeout | Fallback to Ollama or queue for teacher review | Async Retry |

## 5. BDD Acceptance Criteria
- **Given** an exam submission with 5 MCQs and 1 Essay, **When** submitted, **Then** MCQs are graded in $< 100\text{ms}$ and essay evaluation completes with a valid `error_category` tag.
