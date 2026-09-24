# Phase 1 Checklist: Authentication & User Accounts

**Status:** ⬜ Not started · **Owner(s):** _assign_ · **Started:** _date_ · **Finished:** _date_

Plan: [README.md](README.md) · Target window: 2026-11-09 → 2026-11-13

## Database

- [ ] Enums: `Role` (PATIENT, DOCTOR, LAB_TECHNICIAN, ADMIN), `UserStatus` (ACTIVE, PENDING_VERIFICATION, SUSPENDED)
- [ ] Models: `User` (email unique, passwordHash, fullName, phone, role, status), `RefreshToken` (hashed token, expiry, revokedAt)

## Backend

- [ ] `auth` module: register, login, logout, refresh, me, change password
- [ ] Password hashing (bcrypt or argon2); password rules enforced in the DTO
- [ ] JWT access + refresh tokens delivered as httpOnly cookies; refresh tokens stored hashed and rotated
- [ ] `JwtAuthGuard` (global, with `@Public()` opt-out) and `RolesGuard` with `@Roles()` decorator
- [ ] `users` module for admins: list / filter / search users, change status (approve, suspend, reactivate)
- [ ] Seed script creates the first Admin from environment variables

## Frontend

- [ ] `/register` with role selector (Patient, Doctor, Lab Technician) and validated form
- [ ] `/login` form; `/pending-verification` info page
- [ ] Auth slice + `me` query; automatic refresh on 401 in the RTK Query base API
- [ ] Route protection (`middleware.ts`, or `proxy.ts` on Next.js 16+; use whatever your installed version expects)
- [ ] Dashboard shell: role-based sidebar, header with user menu and logout
- [ ] Admin `/admin/users`: table with search, role/status filters, approve / suspend actions
- [ ] GSAP: form entrance, page-transition feel, success/error feedback

## Endpoints working (tested in Swagger)

- [ ] `POST /auth/register` (Public)
- [ ] `POST /auth/login` (Public)
- [ ] `POST /auth/refresh` (Refresh cookie)
- [ ] `POST /auth/logout` (Authenticated)
- [ ] `GET /auth/me` (Authenticated)
- [ ] `PATCH /auth/change-password` (Authenticated)
- [ ] `GET /users` (Admin)
- [ ] `GET /users/:id` (Admin)
- [ ] `PATCH /users/:id/status` (Admin)

## Tests

- [ ] Registering with a duplicate email fails with a clear error
- [ ] Patient registers as ACTIVE; Doctor and Lab Technician register as PENDING_VERIFICATION
- [ ] Login fails for a wrong password, a suspended account and a pending account (different error codes)
- [ ] Expired access token is refreshed silently; a revoked refresh token is rejected
- [ ] A Patient calling an Admin endpoint gets 403; an anonymous call gets 401
- [ ] `passwordHash` never appears in any API response

## Definition of done

- [ ] All three roles can register; Admin can approve and suspend
- [ ] Every non-public endpoint is protected; role checks proven by tests
- [ ] Tokens live only in httpOnly cookies (nothing in localStorage)
- [ ] UC-01, UC-02 and the basic part of UC-14 pass their acceptance check

## Viva / demo preparation

- [ ] Every member can explain: Why httpOnly cookies instead of localStorage tokens (XSS cannot read them)
- [ ] Every member can explain: Why doctors and lab technicians need admin approval (plan constraint: only verified doctors issue prescriptions)
- [ ] Every member can explain: How RBAC works: roles in the token, `RolesGuard` on the route, status re-checked from the database

## Sign-off

- [ ] Reviewed by another team member: ______
- [ ] `develop` merged into `main`
- [ ] Tagged `v0.1.0`
- [ ] `CHANGELOG.md` updated
