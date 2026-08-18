# Feature Specification: Class Workspace Management (`feat-workspace`)

> **Feature Module**: `feat-workspace`  
> **Status**: `Approved`  
> **Use Cases Covered**: `UC004` (Create Workspace), `UC005` (Enroll via Code), `UC006` (Manage Members)  
> **API Endpoints**: #5 (`POST /workspaces`), #6 (`GET /workspaces`), #7 (`POST /workspaces/enroll`), #8 (`GET /workspaces/{id}/members`)

---

## 1. Context & Goal
- **Problem**: Instructors need virtual class spaces to group students, attach subject-specific notebooks, and publish exam papers.
- **Goal**: Allow teachers to create workspaces with auto-generated 6-char `join_code` and students to self-enroll instantly.

## 2. Actors & Permissions
- `TEACHER`: Create, update, view owned workspaces, manage members.
- `STUDENT`: Enroll via join code, view enrolled workspaces.
- `SYSTEM_ADMIN`: Full access to all workspaces.

## 3. Functional Requirements
- **FR-WS-01**: Teachers can create a workspace with `name`, `description`, `subject_code`.
- **FR-WS-02**: System automatically generates a unique 6-character alphanumeric `join_code`.
- **FR-WS-03**: Students enroll by submitting a valid `join_code` (`UC005`).
- **FR-WS-04**: Endpoint `/workspaces` returns paginated list of workspaces owned by or enrolled by the requesting user.
- **FR-WS-05**: Endpoint `/workspaces/{id}/members` returns roster of students and teachers in the workspace.

## 4. Data Model & Entities
- **Tables**: `class_workspaces` (`id`, `name`, `description`, `subject_code`, `join_code`, `teacher_id`, `created_at`), `class_members` (`id`, `workspace_id`, `user_id`, `role`, `joined_at`).
- **DTOs**: `CreateWorkspaceDTO`, `WorkspaceDTO`, `EnrollRequestDTO`, `WorkspaceMemberDTO`.

## 5. Error Handling
| Scenario | Expected Behavior | HTTP Status |
|---|---|---|
| Invalid join code | "Workspace with code not found" | `404 Not Found` |
| Already enrolled in workspace | "User already member of workspace" | `409 Conflict` |
| Student attempting to create workspace | "Access Denied" | `403 Forbidden` |

## 6. BDD Acceptance Criteria
- **Given** an authenticated Teacher, **When** posting to `/workspaces` with `{ name: "CS101" }`, **Then** return 201 with generated 6-char `join_code`.
- **Given** an authenticated Student and code `"ABC123"`, **When** posting to `/workspaces/enroll`, **Then** return 200 with membership confirmation.
