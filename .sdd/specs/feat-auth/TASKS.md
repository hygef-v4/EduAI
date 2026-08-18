# Atomic Tasks: Authentication & User Profile (`feat-auth`)

## Backend Tasks
- [ ] `TASK-AUTH-01`: Create `User` entity, `UserRole` enum, and Flyway `V1__create_users.sql` (Est: 1h)
- [ ] `TASK-AUTH-02`: Implement `UserRepository` with `findByEmail` and `existsByEmail` (Est: 0.5h)
- [ ] `TASK-AUTH-03`: Implement `JwtTokenProvider` (generate, parse, validate tokens) (Est: 1.5h)
- [ ] `TASK-AUTH-04`: Implement `AuthService` (register, login, me, updateProfile) (Est: 2h)
- [ ] `TASK-AUTH-05`: Implement `AuthController` with 4 REST endpoints + validation annotations (Est: 1.5h)
- [ ] `TASK-AUTH-06`: Configure Spring Security filter chain and CORS (Est: 1h)
- [ ] `TASK-AUTH-07`: Write unit & integration tests for Auth endpoints (85%+ coverage) (Est: 2h)

## Frontend Tasks
- [ ] `TASK-AUTH-08`: Create `auth-service.ts` with Axios API calls (Est: 1h)
- [ ] `TASK-AUTH-09`: Implement `useAuth` hook with login/logout and token state (Est: 1.5h)
- [ ] `TASK-AUTH-10`: Build `LoginForm` and `RegisterForm` components with Tailwind (Est: 2h)
- [ ] `TASK-AUTH-11`: Build `/login` and `/register` pages with route protection redirects (Est: 1h)
