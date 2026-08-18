# ADR-007: Client-Side State Synchronization — TanStack Query

## Status: APPROVED

## Context
The student exam taking interface requires frequent silent draft syncing (every 10 seconds), automatic retry upon network drops, optimistic UI updates, and intelligent caching of quiz/notebook lists without manual `useEffect` boilerplate.

## Decision
Adopt **TanStack Query (React Query) v5** as the sole server-state management library in Next.js 14.

## Consequences
- **Positive**: Eliminates Redux boilerplate, provides out-of-the-box window focus refetching, mutation states, query invalidation, and seamless draft sync lifecycle.
- **Negative**: Developers must understand TanStack Query query key management.
