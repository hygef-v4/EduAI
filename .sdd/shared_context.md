# Shared Context & API Contracts — EduAI

> The definitive contract synchronization interface between Frontend (Next.js) and Backend (Spring Boot).

---

## 1. Global API Conventions

- **Base URL**: `/api/v1`
- **Auth Header**: `Authorization: Bearer <JWT_TOKEN>`
- **Content Type**: `application/json; charset=UTF-8` (except file upload endpoints: `multipart/form-data`)

### Response Envelope Structure

```json
{
  "status": 200,
  "message": "Operation successful",
  "data": {},
  "timestamp": "2026-08-18T10:00:00Z"
}
```

### Error Envelope Structure

```json
{
  "status": 400,
  "error": "BAD_REQUEST",
  "message": "Detailed error explanation",
  "details": ["Field 'email' must be a valid email address"],
  "timestamp": "2026-08-18T10:00:00Z"
}
```

---

## 2. Complete 33 REST API Catalog

| # | Method | Endpoint Path | Module | Request Body / Params | Response Data | Allowed Roles |
|---|---|---|---|---|---|---|
| **1** | `POST` | `/auth/register` | Auth | `{ email, password, full_name, role }` | `{ user: { id, email, full_name, role }, token }` | Public |
| **2** | `POST` | `/auth/login` | Auth | `{ email, password }` | `{ user: { id, email, full_name, role }, token }` | Public |
| **3** | `GET` | `/auth/me` | Auth | None | `{ id, email, full_name, role, avatar_url }` | All Roles |
| **4** | `PUT` | `/users/profile` | Auth | `{ full_name, avatar_url }` | `{ id, email, full_name, avatar_url }` | All Roles |
| **5** | `POST` | `/workspaces` | Workspace | `{ name, description, subject_code }` | `{ id, name, join_code, teacher_id }` | Teacher, Admin |
| **6** | `GET` | `/workspaces` | Workspace | `?page=0&size=20` | `Page<WorkspaceDTO>` | All Roles |
| **7** | `POST` | `/workspaces/enroll` | Workspace | `{ join_code }` | `{ workspace_id, role, enrolled_at }` | Student |
| **8** | `GET` | `/workspaces/{id}/members` | Workspace | None | `List<WorkspaceMemberDTO>` | All Roles |
| **9** | `POST` | `/workspaces/{id}/notebooks` | Document RAG | `{ title, description }` | `{ id, title, workspace_id, created_at }` | All Roles |
| **10** | `GET` | `/workspaces/{id}/notebooks` | Document RAG | None | `List<NotebookDTO>` | All Roles |
| **11** | `POST` | `/notebooks/{id}/documents` | Document RAG | Multipart: `file` | `{ id, file_name, file_size, status: "PROCESSING" }` | All Roles |
| **12** | `POST` | `/notebooks/{id}/prompts` | Document RAG | `{ title, content_text }` | `{ id, title, char_count, status: "COMPLETED" }` | All Roles |
| **13** | `POST` | `/notebooks/{id}/scrape-url` | Document RAG | `{ target_url, title }` | `{ id, file_name, status: "PROCESSING" }` | All Roles |
| **14** | `GET` | `/notebooks/{id}/documents` | Document RAG | None | `List<DocumentDTO>` | All Roles |
| **15** | `POST` | `/quiz/generate` | AI Quiz Studio | `{ source_mode, notebook_id?, topic_prompt?, question_count, difficulty_distribution, formats }` | `List<GeneratedQuestionDTO>` | All Roles |
| **16** | `POST` | `/question-banks` | Question Bank | `{ name, subject_code, workspace_id }` | `{ id, name, workspace_id }` | Teacher, Admin |
| **17** | `POST` | `/question-banks/{id}/items` | Question Bank | `{ question_type, prompt, answer_key, options, rubric, chunk_id? }` | `QuestionItemDTO` | Teacher, Admin |
| **18** | `GET` | `/question-banks/{id}/items` | Question Bank | `?type=MCQ&difficulty=MEDIUM` | `Page<QuestionItemDTO>` | Teacher, Admin |
| **19** | `POST` | `/exams` | Exam Paper | `{ title, workspace_id, duration_minutes, exam_mode, start_time, end_time, items: [{ question_id, point_weight }] }` | `ExamPaperDTO` | Teacher, Admin |
| **20** | `GET` | `/exams/{id}` | Exam Paper | None | `ExamPaperDetailDTO` | All Roles |
| **21** | `POST` | `/exams/{id}/start` | Test Execution | None | `{ submission_id, exam: { title, duration_minutes }, questions: [...] }` | Student |
| **22** | `PUT` | `/submissions/{id}/draft` | Test Execution | `{ answers: [{ question_id, selected_option_id?, text_answer? }] }` | `{ saved_at, synced_count }` | Student |
| **23** | `POST` | `/exams/{id}/submit` | Test Execution | `{ answers: [...] }` | `{ submission_id, status: "SUBMITTED", total_score }` | Student |
| **24** | `GET` | `/submissions/{id}/result` | AI Grading | None | `SubmissionResultDTO` (with scores, AI rationales, error categories) | All Roles |
| **25** | `POST` | `/grading/{result_id}/override` | AI Grading | `{ score_override, teacher_feedback, override_reason }` | `GradingResultDTO` | Teacher, Admin |
| **26** | `GET` | `/analytics/student/me` | Analytics | None | `StudentAnalyticsDTO` (GPA velocity, completed exams) | Student |
| **27** | `GET` | `/analytics/student/radar` | Analytics | `?workspace_id={id}` | `SkillRadarDTO` (`topic`, `score_percentage`, `mastery_level`) | Student, Teacher |
| **28** | `GET` | `/analytics/student/mistakes` | Analytics | None | `MistakeMatrixDTO` (counts by `ErrorCategory`) | Student, Teacher |
| **29** | `GET` | `/analytics/class/{id}/histogram` | Analytics | None | `ClassHistogramDTO` (`bins`, `mean`, `median`, `std_dev`) | Teacher, Admin |
| **30** | `GET` | `/admin/ai/config` | Admin | None | `{ active_provider, gemini_model, ollama_model, daily_quota_limit }` | System Admin |
| **31** | `PUT` | `/admin/ai/config` | Admin | `{ active_provider, gemini_model, ollama_model }` | `{ status: "UPDATED", active_provider }` | System Admin |
| **32** | `GET` | `/admin/ai/token-usage` | Admin | `?range=30d` | `List<TokenUsageSummaryDTO>` | System Admin |
| **33** | `DELETE`| `/documents/{id}` | Document RAG | None | `{ status: "PURGED", deleted_chunks_count }` | Teacher, Admin |
