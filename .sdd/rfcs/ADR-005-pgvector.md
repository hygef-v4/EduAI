# ADR-005: Vector Storage Architecture — In-DB pgvector

## Status: APPROVED

## Context
The RAG engine requires storing and searching 768-dimensional vector embeddings of document chunks (Google text-embedding-004 & Ollama nomic-embed-text). A separate dedicated vector database (e.g. Pinecone, Milvus, Qdrant) adds operational complexity and breaks ACID transactional consistency with relational metadata.

## Decision
Use **PostgreSQL 16 with the `pgvector` extension** directly within the primary application database.

## Consequences
- **Positive**: Single database container, zero network sync latency between relational metadata and vectors, ACID transactions on document purge, cosine similarity search via SQL.
- **Negative**: High concurrent vector search queries consume PostgreSQL shared buffers.
