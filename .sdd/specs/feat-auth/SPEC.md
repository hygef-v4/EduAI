# Feature Specification: Authentication & User Profile (`feat-auth`)

> **Feature Module**: `feat-auth`  
> **Status**: `Approved`  
> **Use Cases Covered**: `UC001` (Register), `UC002` (Login), `UC003` (Profile Update), `UC007` (OAuth2 SSO), `UC008` (Logout)  
> **API Endpoints**: #1 (`POST /auth/register`), #2 (`POST /auth/login`), #3 (`GET /auth/me`), #4 (`PUT /users/profile`), #34 (`POST /auth/logout`)

---

## 1. Context & Goal
- **Problem**: The system requires secure, stateless authentication with role-based access control (RBAC) to differentiate between Students, Teachers, Content Admins, and System Admins.
- **Goal**: Enable email/password registration with BCrypt hashing and Google OAuth2 SSO, issuing 24h JWT tokens.

## 2. Actors & Permissions
- **Permitted Roles**: Public (`/register`, `/login`), Authenticated (`/me`, `/profile`, `/logout`).
- **Roles Assigned**: `STUDENT`, `TEACHER` (via public registration); `CONTENT_ADMIN`, `SYSTEM_ADMIN` (via DB seed or admin assignment only).
- **Security Boundary**: Public registration with `SYSTEM_ADMIN` or `CONTENT_ADMIN` role will be rejected with `400 Bad Request`.

## 3. Functional Requirements
- **FR-AUTH-01**: Users can register with valid email, password ($\ge 8$ chars), full name, and role (`STUDENT` or `TEACHER` only).
- **FR-AUTH-02**: Passwords must be hashed using BCrypt before database persistence.
- **FR-AUTH-03**: Login authenticates credentials and returns a signed JWT token containing `userId` and `role`.
- **FR-AUTH-04**: `/auth/me` returns current user profile derived from JWT claims.
- **FR-AUTH-05**: Users can update their `full_name` and `avatar_url`.
- **FR-AUTH-06**: `POST /auth/logout` logs user session termination audit event; client discards JWT token from storage.

## 4. Non-Functional Requirements
- **Performance**: Auth response latency $< 150\text{ms}$.
- **Security**: JWT signed with HMAC-SHA256 (256-bit key). Passwords never returned in response DTOs.

## 5. Data Model & Entities
- **Primary Table**: `users` (`id`, `email`, `password_hash`, `full_name`, `role`, `avatar_url`, `created_at`, `updated_at`).
- **DTOs**: `RegisterRequestDTO`, `LoginRequestDTO`, `AuthResponseDTO`, `UserProfileDTO`, `UpdateProfileRequestDTO`.

## 6. Error Handling
| Scenario | Expected System Behavior | HTTP Status |
|---|---|---|
| Duplicate email registration | Return "Email already exists" | `409 Conflict` |
| Invalid credentials on login | Return "Invalid email or password" | `401 Unauthorized` |
| Expired or malformed JWT | Return "Token expired or invalid" | `401 Unauthorized` |
| Registration with `SYSTEM_ADMIN` or `CONTENT_ADMIN` role | Return "Role not allowed for public registration" | `400 Bad Request` |

## 7. BDD Acceptance Criteria
- **Given** an unregistered email `teacher@fpt.edu.vn`, **When** posting to `/auth/register` with valid payload, **Then** return 201 Created with JWT token and user profile.
- **Given** valid credentials, **When** posting to `/auth/login`, **Then** return 200 OK with valid JWT.

## 8. Out of Scope
- Phone number SMS OTP authentication (deferred).
- Multi-factor authentication (MFA/2FA) hardware tokens.
