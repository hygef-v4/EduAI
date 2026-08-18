# Feature Specification: Exam Builder & Test Execution (`feat-exam-engine`)

> **Feature Module**: `feat-exam-engine`  
> **Status**: `Approved`  
> **Use Cases Covered**: `UC030` (Assemble Exam), `UC032` (Set Timer), `UC040` (Timed Exam Lobby), `UC041` (Question Palette), `UC044` (Auto-Save Draft), `UC049` (Auto-Submit Timeout)  
> **API Endpoints**: #19 (`POST /exams`), #20 (`GET /exams/{id}`), #21 (`POST /exams/{id}/start`), #22 (`PUT /submissions/{id}/draft`), #23 (`POST /exams/{id}/submit`)

---

## 1. Context & Goal
- **Problem**: Online exams must prevent cheating, withstand network drops without data loss, and enforce strict timing.
- **Goal**: Provide exam assembly with difficulty matrix, a timed exam room with 10s auto-save sync, and auto-submit on countdown expiry.

## 2. Functional Requirements
- **FR-EXAM-01**: Teachers assemble exam papers with items, point weights, duration minutes, and availability window (`UC030`).
- **FR-EXAM-02**: Students start an attempt (`POST /exams/{id}/start`) which creates an `exam_submissions` record with start timestamp.
- **FR-EXAM-03**: Client silently syncs draft answers every 10s via `PUT /submissions/{id}/draft` (`BR-14`).
- **FR-EXAM-04**: Client includes rich KaTeX math editor for essay questions (`USA-02`).
- **FR-EXAM-05**: When timer hits `00:00:00`, inputs lock and current draft is auto-submitted (`BR-15`).
- **FR-EXAM-06**: Client logs window blur/tab switch events; $>3$ infractions triggers auto-submission (`BR-16`).

## 3. Data Model & Entities
- **Tables**: `exam_papers`, `exam_items`, `exam_submissions` (`status`: `IN_PROGRESS | SUBMITTED | GRADED`), `submission_answers`.
- **DTOs**: `CreateExamDTO`, `ExamPaperDTO`, `StartExamResponseDTO`, `DraftSyncDTO`, `SubmitExamDTO`.

## 4. Error Handling
| Scenario | Expected Behavior | HTTP Status |
|---|---|---|
| Exam not within active window | "Exam is not currently active" | `403 Forbidden` |
| Attempt already submitted | "Submission already completed" | `409 Conflict` |
| Network disconnect | Cache in LocalStorage, sync on reconnect | Client Cached |

## 5. BDD Acceptance Criteria
- **Given** an exam with 30 min duration, **When** student starts, **Then** return submission ID with countdown deadline.
- **Given** active exam, **When** countdown reaches zero, **Then** backend accepts submission with status `SUBMITTED`.
