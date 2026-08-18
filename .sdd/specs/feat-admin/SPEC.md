# Feature Specification: AI Governance & System Administration (`feat-admin`)

> **Feature Module**: `feat-admin`  
> **Status**: `Approved`  
> **Use Cases Covered**: `UC074` (AI Model Switching), `UC076` (Token Cost Monitor), `UC078` (Rate Limiting), `UC081` (System Logs)  
> **API Endpoints**: #30 (`GET /admin/ai/config`), #31 (`PUT /admin/ai/config`), #32 (`GET /admin/ai/token-usage`)

---

## 1. Context & Goal
- **Problem**: Administrators need to manage AI operational costs, switch between primary Gemini and local Ollama, and monitor quota consumption across classes.
- **Goal**: Provide real-time token tracking, dynamic `LlmRouter` runtime configuration, and daily user request quota enforcement.

## 2. Actors & Permissions
- **Permitted Roles**: `SYSTEM_ADMIN` exclusively.

## 3. Functional Requirements
- **FR-ADM-01**: Admin can retrieve and update active AI provider (`GEMINI_2_FLASH` $\leftrightarrow$ `OLLAMA_LOCAL`) at runtime without restarting the server.
- **FR-ADM-02**: System logs prompt tokens, completion tokens, and estimated USD cost for every AI invocation in `token_usage_logs`.
- **FR-ADM-03**: Endpoint `/admin/ai/token-usage` returns aggregated token and cost trends over 7d, 30d, 90d periods.
- **FR-ADM-04**: Enforce role-based daily token rate limits (`BR-26`).

## 4. Data Model & Entities
- **Tables**: `token_usage_logs` (`id`, `user_id`, `provider`, `prompt_tokens`, `completion_tokens`, `cost_usd`, `created_at`).
- **DTOs**: `AiConfigDTO`, `TokenUsageSummaryDTO`.

## 5. BDD Acceptance Criteria
- **Given** an authenticated Admin, **When** updating `/admin/ai/config` to `OLLAMA_LOCAL`, **Then** subsequent AI generation calls route through the local Ollama client.
- **Given** 100 student quiz generation calls, **When** querying `/admin/ai/token-usage`, **Then** return accurate sum of prompt/completion tokens and estimated USD expenditure.
