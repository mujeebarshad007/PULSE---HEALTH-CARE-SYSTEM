# Phase 1: Authentication & User Accounts

> **Increment:** Increment 1 (Sprint 1) · **Target window:** 2026-11-09 → 2026-11-13 · **Status:** ⬜ Not started

> This document is a plan. It contains **no application code**.

## Goal

Anyone can register with a role, log in securely, and land on a role-specific dashboard shell. Admin can list users, approve doctors / lab technicians, and suspend accounts.

## Use cases covered

- UC-01
- UC-02
- UC-14 (basic)

**Roles involved:** Guest, Patient, Doctor, Lab Technician, Admin

## Decisions made for this phase

- Registration collects `fullName` and `phone` on `User`; role profiles are added in Phase 2 (keeps phases independent).
- Pending accounts cannot log in; they see `/pending-verification` right after registering.

Change these here first if the team disagrees, then update the checklist.

## Scope

### Database

- Enums: `Role` (PATIENT, DOCTOR, LAB_TECHNICIAN, ADMIN), `UserStatus` (ACTIVE, PENDING_VERIFICATION, SUSPENDED)
- Models: `User` (email unique, passwordHash, fullName, phone, role, status), `RefreshToken` (hashed token, expiry, revokedAt)

### Backend

- `auth` module: register, login, logout, refresh, me, change password
- Password hashing (bcrypt or argon2); password rules enforced in the DTO
- JWT access + refresh tokens delivered as httpOnly cookies; refresh tokens stored hashed and rotated
- `JwtAuthGuard` (global, with `@Public()` opt-out) and `RolesGuard` with `@Roles()` decorator
- `users` module for admins: list / filter / search users, change status (approve, suspend, reactivate)
- Seed script creates the first Admin from environment variables

### Frontend

- `/register` with role selector (Patient, Doctor, Lab Technician) and validated form
- `/login` form; `/pending-verification` info page
- Auth slice + `me` query; automatic refresh on 401 in the RTK Query base API
- Route protection (`middleware.ts`, or `proxy.ts` on Next.js 16+; use whatever your installed version expects)
- Dashboard shell: role-based sidebar, header with user menu and logout
- Admin `/admin/users`: table with search, role/status filters, approve / suspend actions
- GSAP: form entrance, page-transition feel, success/error feedback

## API endpoints

Base path `/api/v1`. Conventions: [`api-design.md`](../../architecture/api-design.md). Permissions: [`roles-and-permissions.md`](../../project/roles-and-permissions.md).

| Method | Path | Access | Use case |
| --- | --- | --- | --- |
| `POST` | `/auth/register` | Public | UC-01 |
| `POST` | `/auth/login` | Public | UC-02 |
| `POST` | `/auth/refresh` | Refresh cookie | UC-02 |
| `POST` | `/auth/logout` | Authenticated | UC-02 |
| `GET` | `/auth/me` | Authenticated | UC-02 |
| `PATCH` | `/auth/change-password` | Authenticated | UC-02 |
| `GET` | `/users` | Admin | UC-14 |
| `GET` | `/users/:id` | Admin | UC-14 |
| `PATCH` | `/users/:id/status` | Admin | UC-14 |

## Files in this phase

Every file below already exists in the repo as a placeholder tagged with this phase. Fill them in; do not move them.

<details>
<summary><b>Backend</b> (25 files)</summary>

| File | Purpose |
| --- | --- |
| `backend/prisma/seed.ts` | Seed: admin (P1), specializations / symptoms / diseases (P2), demo labs (P5), demo data (P6) |
| `backend/src/common/decorators/public.decorator.ts` | @Public(): opt an endpoint out of the global JWT guard |
| `backend/src/common/decorators/roles.decorator.ts` | @Roles(...): declare which roles may call an endpoint |
| `backend/src/common/decorators/current-user.decorator.ts` | @CurrentUser(): inject the authenticated user into a handler |
| `backend/src/common/guards/jwt-auth.guard.ts` | Global guard: require a valid access token unless @Public() |
| `backend/src/common/guards/roles.guard.ts` | Global guard: enforce @Roles() against the user's role |
| `backend/src/common/interfaces/authenticated-user.interface.ts` | Shape of req.user (id, role, status) |
| `backend/src/modules/auth/auth.module.ts` | Authentication - module wiring |
| `backend/src/modules/auth/auth.controller.ts` | Authentication - HTTP routes, Swagger docs, role decorators |
| `backend/src/modules/auth/auth.service.ts` | Authentication - business logic and database access |
| `backend/src/modules/auth/auth.service.spec.ts` | Authentication - unit tests for the service |
| `backend/src/modules/auth/dto/register.dto.ts` | Register payload: email, password, fullName, phone, role (patient / doctor / lab technician only) |
| `backend/src/modules/auth/dto/login.dto.ts` | Login payload: email, password |
| `backend/src/modules/auth/dto/change-password.dto.ts` | Change password payload |
| `backend/src/modules/auth/strategies/jwt.strategy.ts` | Validate access token; load user and check status on every request |
| `backend/src/modules/auth/strategies/jwt-refresh.strategy.ts` | Validate and rotate refresh token |
| `backend/src/modules/auth/interfaces/jwt-payload.interface.ts` | Token payload shape |
| `backend/src/modules/users/users.module.ts` | Admin user management - module wiring |
| `backend/src/modules/users/users.controller.ts` | Admin user management - HTTP routes, Swagger docs, role decorators |
| `backend/src/modules/users/users.service.ts` | Admin user management - business logic and database access |
| `backend/src/modules/users/users.service.spec.ts` | Admin user management - unit tests for the service |
| `backend/src/modules/users/dto/update-user-status.dto.ts` | Status change: ACTIVE / SUSPENDED (approve pending accounts) |
| `backend/src/modules/users/dto/list-users-query.dto.ts` | Filters: role, status, search text |
| `backend/test/auth.e2e-spec.ts` | E2E: register, login, refresh, logout, role guard |
| `backend/test/users.e2e-spec.ts` | E2E: admin approve / suspend |

</details>

<details>
<summary><b>Frontend</b> (33 files)</summary>

| File | Purpose |
| --- | --- |
| `frontend/src/app/(auth)/layout.tsx` | Centered auth layout |
| `frontend/src/app/(auth)/login/page.tsx` | Login page |
| `frontend/src/app/(auth)/register/page.tsx` | Register page with role selector |
| `frontend/src/app/(auth)/pending-verification/page.tsx` | Shown to doctors / lab technicians awaiting admin approval |
| `frontend/src/app/(dashboard)/layout.tsx` | Protected layout: role-based sidebar + header |
| `frontend/src/app/(dashboard)/patient/page.tsx` | Patient dashboard (upcoming items grow with each phase) |
| `frontend/src/app/(dashboard)/doctor/page.tsx` | Doctor dashboard |
| `frontend/src/app/(dashboard)/admin/page.tsx` | Admin dashboard |
| `frontend/src/app/(dashboard)/admin/users/page.tsx` | Users table: search, filters, approve / suspend |
| `frontend/src/middleware.ts` | Route protection by login state and role (file is named proxy.ts on Next.js 16+; use what your version expects) |
| `frontend/src/components/layout/sidebar.tsx` | Role-based dashboard sidebar |
| `frontend/src/components/layout/dashboard-header.tsx` | Dashboard header with user menu |
| `frontend/src/components/layout/mobile-nav.tsx` | Mobile navigation drawer |
| `frontend/src/components/common/data-table.tsx` | Reusable table with pagination and sorting |
| `frontend/src/components/common/confirm-dialog.tsx` | Confirm destructive actions |
| `frontend/src/components/common/pagination-controls.tsx` | Page navigation control |
| `frontend/src/components/common/role-guard.tsx` | Hide UI by role (UX only; real security is on the API) |
| `frontend/src/features/auth/components/login-form.tsx` | Login form |
| `frontend/src/features/auth/components/register-form.tsx` | Register form with role-specific fields |
| `frontend/src/features/auth/components/role-selector.tsx` | Role picker |
| `frontend/src/features/auth/api/auth.api.ts` | RTK Query endpoints for auth |
| `frontend/src/features/auth/types.ts` | TypeScript types for auth (mirror the API DTOs) |
| `frontend/src/features/auth/auth.slice.ts` | Auth state (current user, status) |
| `frontend/src/features/auth/schemas.ts` | Zod schemas for login / register |
| `frontend/src/features/auth/hooks/use-auth.ts` | Hook exposing user, role, logout |
| `frontend/src/features/users/components/users-table.tsx` | Admin users table |
| `frontend/src/features/users/components/user-status-actions.tsx` | Approve / suspend / reactivate menu |
| `frontend/src/features/users/api/users.api.ts` | RTK Query endpoints for users |
| `frontend/src/features/users/types.ts` | TypeScript types for users (mirror the API DTOs) |
| `frontend/src/lib/api-error.ts` | Turn API error responses into user-friendly messages |
| `frontend/src/hooks/use-media-query.ts` | Responsive helper |
| `frontend/src/types/enums.ts` | Role, status and other enums mirrored from the backend |
| `frontend/src/config/navigation.ts` | Sidebar items per role |

</details>

## Test scenarios

- Registering with a duplicate email fails with a clear error
- Patient registers as ACTIVE; Doctor and Lab Technician register as PENDING_VERIFICATION
- Login fails for a wrong password, a suspended account and a pending account (different error codes)
- Expired access token is refreshed silently; a revoked refresh token is rejected
- A Patient calling an Admin endpoint gets 403; an anonymous call gets 401
- `passwordHash` never appears in any API response

## Definition of done

- [ ] All three roles can register; Admin can approve and suspend
- [ ] Every non-public endpoint is protected; role checks proven by tests
- [ ] Tokens live only in httpOnly cookies (nothing in localStorage)
- [ ] UC-01, UC-02 and the basic part of UC-14 pass their acceptance check

## Be ready to explain (viva / demo)

- Why httpOnly cookies instead of localStorage tokens (XSS cannot read them)
- Why doctors and lab technicians need admin approval (plan constraint: only verified doctors issue prescriptions)
- How RBAC works: roles in the token, `RolesGuard` on the route, status re-checked from the database

## Stretch goals (not part of the MVP)

- Forgot / reset password by email
- Email verification
- Account lock after repeated failed logins

## Vibe-coding prompt

Paste into your AI assistant after loading `AGENTS.md`. More templates: [`vibe-coding/workflow.md`](../../vibe-coding/workflow.md).

```text
Read `AGENTS.md` and `docs/phases/phase-1-authentication/README.md` first.

Implement ONLY Authentication & User Accounts (Phase 1). Do not build anything from later phases.

Work in this order and stop after each step so I can test it:
1. Database: Prisma models + migration for this phase.
2. Backend: module, DTOs, service, controller, guards, unit tests, Swagger decorators.
3. Frontend: RTK Query endpoints, pages, components, forms, GSAP touches.
4. Tick the boxes in `docs/phases/phase-1-authentication/checklist.md` for everything that works.

Login for PENDING accounts must return a specific error code. Seed the Admin from env vars.
```

---

← [Phase 0: Foundation & Setup](../phase-0-foundation/README.md) · [Phase 2: Profiles & Disease Information](../phase-2-profiles-and-disease-info/README.md) → · [Checklist](checklist.md)
