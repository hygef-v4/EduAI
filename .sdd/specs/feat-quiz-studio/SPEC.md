# Feature Specification: AI Quiz Studio & Question Generation (`feat-quiz-studio`)

> **Feature Module**: `feat-quiz-studio`  
> **Status**: `Approved`  
> **Use Cases Covered**: `UC016` (Dual Mode Gen), `UC017` (MCQ Gen), `UC018` (True/False Gen), `UC019` (Essay Gen), `UC020` (Citation Verification), `UC027` (Question Bank), `UC028` (Edit Item), `UC029` (Save to Bank)  
> **API Endpoints**: #15 (`POST /quiz/generate`), #16 (`POST /question-banks`), #17 (`POST /question-banks/{id}/items`), #18 (`GET /question-banks/{id}/items`), #35 (`POST /question-banks/{id}/items/batch`)

---

## 1. Context & Goal
- **Problem**: Manual question writing is tedious and prone to generic out-of-syllabus questions.
- **Goal**: Generate grounded assessments from Notebook RAG or Direct Topic Prompts with Draft-07 JSON Schema validation and zero-hallucination citations.

## 2. Functional Requirements
- **FR-QUIZ-01**: Support two generation modes: `FROM_NOTEBOOK` (hybrid search over notebook chunks) and `DIRECT_TOPIC_PROMPT` (direct prompt string).
- **FR-QUIZ-02**: Generate Multiple Choice (4 options, 1 correct), True/False, and Short Essay questions.
- **FR-QUIZ-03**: Enforce Draft-07 JSON Schema validation on all LLM responses (`BR-11`).
- **FR-QUIZ-04**: In `FROM_NOTEBOOK` mode, mandate non-null `chunk_id` and exact source quote (`BR-10`).
- **FR-QUIZ-05**: Allow teachers to edit generated items and save them into the Question Bank (`UC027`, `UC029`).
- **FR-QUIZ-06**: `POST /question-banks/{id}/items/batch` persists an array of reviewed/edited question items atomically into the specified question bank.

## 3. Data Model & Entities
- **Tables**: `question_banks`, `question_items` (`topic VARCHAR(100)`, `question_type`, `prompt`, `difficulty`, `answer_key`, `rubric`, `chunk_id` nullable), `question_options` (`option_text`, `is_correct`).
- **DTOs**: `QuizGenerateRequestDTO`, `GeneratedQuestionDTO` (`topic`, `prompt`, `options`, `answer_key`, `rubric`, `chunk_id`, `exact_quote`), `QuestionBankDTO`, `QuestionItemDTO`.

## 4. Error Handling
| Scenario | Expected Behavior | HTTP Status |
|---|---|---|
| Invalid JSON from LLM | Trigger Repair Prompt $\rightarrow$ fallback Ollama | Internal Retry |
| Direct prompt $< 30$ chars | Reject with validation message | `400 Bad Request` |
| Empty notebook selected | "Notebook contains no completed documents" | `400 Bad Request` |

## 5. BDD Acceptance Criteria
- **Given** a notebook with vectorized documents, **When** requesting 5 MCQs via `/quiz/generate`, **Then** return 5 questions with verified `chunk_id` and exact quotes in $< 8.0\text{s}$.
