# ADR-006: Dual Document Ingestion Model

## Status: APPROVED

## Context
Educators frequently want to generate assessments not only from uploaded formal documents (PDF, DOCX) but also by directly typing or pasting topic summaries, notes, or ad-hoc lecture prompts without creating a physical file.

## Decision
Support two ingestion workflows in the RAG pipeline:
1. **File Ingestion**: File Upload (PDF, DOCX, TXT) $\rightarrow$ Text Extraction $\rightarrow$ Chunking $\rightarrow$ Embedding.
2. **Direct Text Ingestion**: Direct Text/Notes (30–20,000 chars) $\rightarrow$ Direct Ingestion $\rightarrow$ Chunking $\rightarrow$ Embedding.

Both workflows produce uniform records in `documents` and `document_chunks`.

## Consequences
- **Positive**: Exceptional user flexibility, rapid prototyping of quizzes, uniform downstream RAG retrieval.
