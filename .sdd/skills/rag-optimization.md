# Skill Playbook: RAG Engine Optimization — EduAI

> Senior engineering playbook for tuning document ingestion, vector retrieval, and grounding accuracy.

---

## 1. Chunking Strategy
- **Token Limit**: Strict 512 tokens using Tiktoken / BERT tokenizer approximation.
- **Overlap**: 10% (approx 51 tokens) to prevent boundary context clipping.
- **Header Injection**: Always prepend document title and section breadcrumbs to each chunk:
  ```
  [Doc: CS101_Chapter1.pdf | Section: 1.2 Binary Search Trees]
  <chunk content here...>
  ```

## 2. PostgreSQL `pgvector` Query Optimization
- **Index Type**: Use HNSW (Hierarchical Navigable Small World) index for high-speed similarity search:
  ```sql
  CREATE INDEX ON document_chunks USING hnsw (embedding vector_cosine_ops) WITH (m = 16, ef_construction = 64);
  ```
- **Similarity Metric**: Cosine Distance (`<=>` operator). Score formula: `similarity = 1 - (embedding <=> :queryEmbedding)`.
- **Top-K Selection**: Default `K = 5` chunks. Minimum similarity threshold: `0.70`.

## 3. Grounded Citation Verification
Before returning AI-generated questions to users, verify that the returned `exact_quote` exists as a case-insensitive substring of the referenced `document_chunks.content`.
