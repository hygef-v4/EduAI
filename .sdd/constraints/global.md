# Global Constraints — EduAI

> Tech stack, naming conventions, coding standards, and cross-cutting rules.

---

## Tech Stack (Locked)

| Layer | Technology | Version | Lock Reason |
|---|---|---|---|
| Backend Language | Java | 21 (LTS) | Team expertise, Spring Boot compatibility |
| Backend Framework | Spring Boot | 3.3.x | Latest stable, Hibernate 6, Jakarta EE 10 |
| Frontend Framework | Next.js (App Router) | 14.x | React Server Components, SEO, routing |
| Frontend Language | TypeScript | 5.x (strict) | Type safety |
| CSS Framework | TailwindCSS | 3.x | Utility-first, responsive, no CSS overhead |
| State Management | TanStack Query | v5 | Automatic caching, background re-fetch |
| HTTP Client | Axios | Latest | Interceptors for JWT, response transform |
| Database | PostgreSQL | 16 | Relational + pgvector in single instance |
| Vector Extension | pgvector | Latest | 1536-dim embeddings, HNSW index |
| DB Migration | Flyway | Latest | SQL-based, deterministic |
| Build Tool | Maven | 3.9+ | Standard Java build tool |
| Package Manager | npm | 10+ | Node.js package management |
| Containerization | Docker + Compose | v3.9 spec | Local dev + defense demo |

## Naming Conventions

### Java (Backend)
- **Packages**: `com.eduai.{layer}` — lowercase, singular (`controller`, `service`, `entity`)
- **Classes**: PascalCase (`AuthController`, `WorkspaceService`, `UserRole`)
- **Methods**: camelCase (`findByEmail`, `generateQuiz`, `gradeEssay`)
- **Constants**: UPPER_SNAKE_CASE (`MAX_CHUNK_SIZE`, `JWT_EXPIRY_HOURS`)
- **Database columns**: snake_case (`created_at`, `join_code`, `password_hash`)

### TypeScript (Frontend)
- **Files**: kebab-case (`api-client.ts`, `use-auth.ts`, `quiz-config.tsx`)
- **Components**: PascalCase files for React components (`LoginForm.tsx`, `SkillRadar.tsx`)
- **Interfaces/Types**: PascalCase with `I` prefix only for interfaces extending external types
- **Hooks**: camelCase with `use` prefix (`useAuth`, `useAutoSave`, `useCountdown`)
- **Routes**: kebab-case directories (`quiz-studio/`, `exam-builder/`)

### Git
- **Branches**: `feature/{module-name}` (e.g., `feature/rag-engine`, `feature/exam-builder`)
- **Commits**: Conventional Commits format: `type(scope): description`
  - Types: `feat`, `fix`, `docs`, `refactor`, `test`, `chore`, `ci`
  - Example: `feat(quiz): add dual-mode question generation endpoint`

## Coding Standards

### Backend (Java)
- Use Lombok `@Data`, `@Builder`, `@AllArgsConstructor` for DTOs and Entities.
- Never expose JPA entities directly in REST responses — always map to DTOs.
- Use `Optional<>` return types for repository `findBy*` methods.
- Use `@Transactional` on service methods that perform write operations.
- Log with SLF4J (`@Slf4j`), never `System.out.println`.

### Frontend (TypeScript)
- Use functional components only — no class components.
- Use TanStack Query for all server state — no manual `useEffect` + `fetch`.
- Use `'use client'` directive only when component needs browser APIs or state.
- Import paths use `@/` alias mapped to `src/`.

### API Design
- Base path: `/api/v1/`
- Response envelope: `{ "data": ..., "message": "...", "timestamp": "..." }`
- Error envelope: `{ "error": "ERROR_CODE", "message": "...", "details": [...] }`
- Pagination: `?page=0&size=20&sort=createdAt,desc`
- All timestamps: ISO 8601 UTC (`2026-08-18T10:00:00Z`)
