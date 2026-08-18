# Feature Specification: Document Ingestion & RAG Engine (`feat-document-rag`)

> **Feature Module**: `feat-document-rag`  
> **Status**: `Approved`  
> **Use Cases Covered**: `UC009` (Ingest Prompt), `UC010` (Upload Document), `UC011` (Scrape URL), `UC012` (Chunk/Embed), `UC013` (Hybrid Search), `UC014` (Purge Document), `UC015` (Manage Notebooks)  
> **API Endpoints**: #9 (`POST /workspaces/{id}/notebooks`), #10 (`GET /workspaces/{id}/notebooks`), #11 (`POST /notebooks/{id}/documents`), #12 (`POST /notebooks/{id}/prompts`), #13 (`POST /notebooks/{id}/scrape-url`), #14 (`GET /notebooks/{id}/documents`), #33 (`DELETE /documents/{id}`)

---

## 1. Context & Goal
- **Problem**: Instructors need to supply course materials in various forms (PDF, DOCX, TXT, Web URLs, direct text notes) so the AI can ground question generation strictly in course facts without hallucination.
- **Goal**: Ingest documents, split into 512-token chunks, compute 1536-dim embeddings via `pgvector`, and expose hybrid search retrieval `< 250ms`.

## 2. Functional Requirements
- **FR-RAG-01**: Users can create Notebooks under workspaces (`UC015`).
- **FR-RAG-02**: File upload accepts PDF, DOCX, TXT $\le 25\text{MB}$ (`UC010`).
- **FR-RAG-03**: Direct Text Ingestion accepts raw text prompts/notes (30–20,000 chars) (`UC009`).
- **FR-RAG-04**: URL Scraping extracts readable body text from public web links (`UC011`).
- **FR-RAG-05**: Background worker (`VectorizationWorker`) extracts text, splits into 512 tokens with 10% overlap, generates 1536-dim embeddings, and persists to `document_chunks`.
- **FR-RAG-06**: Deleting a document purges all associated chunks and vector embeddings atomically (`UC014`, API #33).

## 3. Data Model & Entities
- **Tables**: `notebooks`, `documents` (`status`: `PROCESSING | COMPLETED | FAILED`), `document_chunks` (`content`, `token_count`, `embedding vector(1536)`).
- **DTOs**: `CreateNotebookDTO`, `NotebookDTO`, `DocumentDTO`, `DirectPromptDTO`, `ChunkSearchResultDTO`.

## 4. Error Handling & Edge Cases
| Scenario | Expected Behavior | HTTP Status |
|---|---|---|
| File $> 25\text{MB}$ | Reject upload with size limit message | `413 Payload Too Large` |
| Text prompt $< 30$ chars | Reject with minimum length error | `400 Bad Request` |
| Vectorization worker failure | Set document `status = FAILED` with error log | Async / Internal Log |

## 5. BDD Acceptance Criteria
- **Given** a 10-page PDF uploaded to a notebook, **When** vectorization completes, **Then** `document_chunks` contains rows with non-null `embedding` and document status is `COMPLETED`.
- **Given** a direct text prompt of 500 words, **When** submitted to `/notebooks/{id}/prompts`, **Then** return 201 Created and chunks are queryable in vector search.
