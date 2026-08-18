# Implementation Plan & Tasks: AI Grading (`feat-grading`)

## Tasks Breakdown
- [ ] `TASK-GRADE-01`: Create `AiGradingResult` and `MistakeLog` JPA entities + Flyway `V7` (Est: 1.5h)
- [ ] `TASK-GRADE-02`: Define Draft-07 JSON Schema for Essay Grading & Error Category outputs (Est: 1h)
- [ ] `TASK-GRADE-03`: Implement `GradingService` (objective exact-match grading + essay prompt builder) (Est: 2.5h)
- [ ] `TASK-GRADE-04`: Implement `@Async AutoGradingWorker` with `LlmRouter` and retry/fallback (Est: 2h)
- [ ] `TASK-GRADE-05`: Implement Teacher Override logic with audit trail logging (Est: 1.5h)
- [ ] `TASK-GRADE-06`: Frontend Instant Grade Result view with score cards, AI diagnostic reasoning, and error tag chips (Est: 2.5h)
- [ ] `TASK-GRADE-07`: Frontend Teacher Gradebook review and Override Modal (Est: 2h)
