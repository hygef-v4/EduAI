# Implementation Plan: Authentication & User Profile (`feat-auth`)

## Architecture Notes
- Spring Security filter chain with `JwtAuthFilter` reading `Authorization: Bearer` header.
- Password hashing with `BCryptPasswordEncoder` (strength 12).
- Frontend stores JWT in localStorage / secure cookie and attaches via Axios interceptor in `services/api-client.ts`.

## Code Files to Create / Touch
- Backend:
  - `entity/User.java`
  - `enums/UserRole.java`
  - `repository/UserRepository.java`
  - `service/AuthService.java`
  - `service/impl/AuthServiceImpl.java`
  - `controller/AuthController.java`
  - `security/JwtTokenProvider.java`
  - `security/JwtAuthFilter.java`
  - `security/SecurityConfig.java`
  - `dto/auth/*.java`
- Frontend:
  - `app/(auth)/login/page.tsx`
  - `app/(auth)/register/page.tsx`
  - `components/auth/LoginForm.tsx`
  - `components/auth/RegisterForm.tsx`
  - `hooks/use-auth.ts`
  - `services/auth-service.ts`
