# Implementation Plan & Tasks: AI Governance (`feat-admin`)

## Tasks Breakdown
- [ ] `TASK-ADM-01`: Create `TokenUsageLog` JPA entity + Flyway `V8` (Est: 1h)
- [ ] `TASK-ADM-02`: Implement `TokenTrackerService` (intercept and log LLM token metrics) (Est: 1.5h)
- [ ] `TASK-ADM-03`: Implement `AdminService` (get/update AI config, token usage aggregations, quota reset) (Est: 2h)
- [ ] `TASK-ADM-04`: Implement `AdminController` with strict `@PreAuthorize("hasRole('SYSTEM_ADMIN')")` (Est: 1.5h)
- [ ] `TASK-ADM-05`: Frontend AI Admin Dashboard (Provider toggle, Token usage area charts, Cost estimator) (Est: 2.5h)
