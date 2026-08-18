# Implementation Plan & Tasks: AI Quiz Studio (`feat-quiz-studio`)

## Tasks Breakdown
- [ ] `TASK-QUIZ-01`: Create `QuestionBank`, `QuestionItem`, `QuestionOption` JPA entities + Flyway `V5` (Est: 2h)
- [ ] `TASK-QUIZ-02`: Define Draft-07 JSON Schemas for MCQ, True/False, and Essay response formats (Est: 1h)
- [ ] `TASK-QUIZ-03`: Implement `PromptTemplateService` for Grounded RAG & Direct Topic Generation (Est: 1.5h)
- [ ] `TASK-QUIZ-04`: Implement `QuizGenerationService` with `LlmRouter` and JSON Schema validation gate (Est: 2.5h)
- [ ] `TASK-QUIZ-05`: Implement `QuestionBankService` (CRUD for question banks and verified items) (Est: 1.5h)
- [ ] `TASK-QUIZ-06`: Implement `QuizController` and `QuestionBankController` (Est: 1.5h)
- [ ] `TASK-QUIZ-07`: Frontend AI Quiz Studio generator interface with live preview & Question Bank tab (Est: 3h)
