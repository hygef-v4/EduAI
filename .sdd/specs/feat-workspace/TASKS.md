# Implementation Plan & Tasks: Class Workspace (`feat-workspace`)

## Architecture Notes
- `WorkspaceService` handles transactional creation of `class_workspaces` and initial teacher enrollment in `class_members`.
- Random alphanumeric generator produces unique `join_code` with collision-retry loop.

## Tasks Breakdown
- [ ] `TASK-WS-01`: Create `ClassWorkspace` and `ClassMember` JPA entities + Flyway `V2__create_workspaces.sql` (Est: 1h)
- [ ] `TASK-WS-02`: Create `ClassWorkspaceRepository` and `ClassMemberRepository` (Est: 0.5h)
- [ ] `TASK-WS-03`: Implement `WorkspaceService` (create, list, enroll, getMembers) (Est: 2h)
- [ ] `TASK-WS-04`: Implement `WorkspaceController` with 4 REST endpoints (Est: 1.5h)
- [ ] `TASK-WS-05`: Write unit and integration tests (Est: 1.5h)
- [ ] `TASK-WS-06`: Frontend `/workspace` dashboard page with workspace cards & Enroll modal (Est: 2h)
- [ ] `TASK-WS-07`: Frontend `/workspace/[id]` detail page with tabs for Notebooks, Exams, Members (Est: 2h)
