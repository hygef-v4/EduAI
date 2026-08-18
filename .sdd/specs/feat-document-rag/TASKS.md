# Implementation Plan & Tasks: Document Ingestion & RAG (`feat-document-rag`)

## Tasks Breakdown
- [ ] `TASK-RAG-01`: Create `Notebook`, `Document`, `DocumentChunk` entities + Flyway `V3` and `V4` (with `pgvector` extension) (Est: 2h)
- [ ] `TASK-RAG-02`: Create text extractors for PDF (PDFBox), DOCX (POI), TXT, and Web scraper (Jsoup) (Est: 2h)
- [ ] `TASK-RAG-03`: Implement `TextChunker` (512 tokens, 10% overlap) (Est: 1h)
- [ ] `TASK-RAG-04`: Implement `EmbeddingService` integrating with Gemini `text-embedding-004` (Est: 1.5h)
- [ ] `TASK-RAG-05`: Implement `@Async VectorizationWorker` for background chunking & embedding (Est: 2h)
- [ ] `TASK-RAG-06`: Implement `DocumentRepository` with native pgvector cosine similarity search (`<=>` operator) (Est: 1.5h)
- [ ] `TASK-RAG-07`: Implement `DocumentController` with 7 REST endpoints (Est: 2h)
- [ ] `TASK-RAG-08`: Frontend Notebook view with File Uploader, Direct Text Box, and Chunking Progress bar (Est: 2.5h)
