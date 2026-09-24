# Authentication & Security

## Roles

`PATIENT`, `DOCTOR`, `LAB_TECHNICIAN`, `ADMIN`. Full matrix: [`../project/roles-and-permissions.md`](../project/roles-and-permissions.md).

## Registration and account status

| Role | Registers as | Can log in |
| --- | --- | --- |
| Patient | ACTIVE | Immediately |
| Doctor | PENDING_VERIFICATION | After an admin approves and verifies the profile |
| Lab technician | PENDING_VERIFICATION | After an admin approves |
| Admin | Created by the seed script only | Immediately |

Suspended users cannot log in and existing sessions stop working (status is re-checked from the database on every request).

## Login flow

1. `POST /auth/login` with email and password.
2. The API verifies the password hash and the account status.
3. It sets two **httpOnly** cookies: a short-lived **access token** and a longer-lived **refresh token** (lifetimes are configuration values).
4. The frontend never sees the tokens. It calls `GET /auth/me` to learn the current user.
5. On a 401, the RTK Query base API calls `POST /auth/refresh` once, then retries the original request.
6. `POST /auth/logout` revokes the refresh token and clears the cookies.

Refresh tokens are stored **hashed** in the database and **rotated** on every refresh. Reuse of a revoked token is rejected.

## Cookies and the `/api` proxy

- Flags: `HttpOnly`, `Secure` (production), `SameSite=Lax`, scoped path.
- Frontend and backend may be hosted on different domains. Cross-site cookies are increasingly blocked by browsers, so the frontend proxies `/api/*` to the backend with a Next.js rewrite. The browser then only talks to one origin and cookies stay first-party.
- Because auth uses cookies, state-changing routes never use `GET`, CORS allows only the frontend origin, and `SameSite=Lax` blocks cross-site form posts.

## Authorization: three layers

1. **Authentication:** global `JwtAuthGuard` (unless `@Public()`).
2. **Role:** `@Roles(...)` enforced by `RolesGuard`.
3. **Ownership / relationship:** checked in services.
   - Patients only access their own records.
   - Doctors only access their own appointments, consultations and prescriptions.
   - A doctor may view a patient's history and lab results only with at least one CONFIRMED or COMPLETED appointment with that patient.
   - Lab technicians only access bookings and results of their own lab.
   - Admins can manage users and reference data but **cannot read diagnoses, prescriptions or lab files**.

## Data protection

- Passwords: bcrypt or argon2 with a strong policy (minimum length, not only letters).
- Never return `passwordHash`, token hashes or storage keys.
- Lab files: random storage keys, allow-list of types (PDF, PNG, JPG), size limit, no public URLs, streamed through an authenticated endpoint that re-checks permissions.
- Audit log (Phase 6): who opened which diagnosis, prescription or lab result, and when.
- Fake data only in development, demos and screenshots.

## Input and transport

- DTO validation with `whitelist` and `forbidNonWhitelisted`.
- Parameterised queries through Prisma (no string-built SQL).
- Helmet security headers, rate limiting (stricter on login and register), request size limits.
- HTTPS everywhere in staging and production.

## Payments

- The app never receives or stores card numbers; the provider handles that.
- Payment status changes only from a **signature-verified** provider callback (or the mock provider in development).
- Callbacks are idempotent: processing the same event twice must not pay twice.

## Secrets

- Only `.env.example` files are committed.
- Different secrets per environment; rotate if leaked.

## Security checklist before the demo

- [ ] Every endpoint × every role tested (RBAC matrix test)
- [ ] No token in `localStorage` / `sessionStorage`
- [ ] Unverified doctors invisible publicly
- [ ] Lab file URLs not reachable without login
- [ ] Rate limit active on auth routes
- [ ] No secrets in the repo history
- [ ] Production cookies are `Secure`
