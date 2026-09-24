# Phase 6: Integration, Testing & Deployment

> **Increment:** Increment 6 · **Target window:** 2026-12-08 → 2026-12-16 (submission 2026-12-17, presentation 2026-12-18) · **Status:** ⬜ Not started

> This document is a plan. It contains **no application code**.

## Goal

Everything works together, is tested, secured and deployed on a public URL with demo data, and the team is ready to present and defend it.

## Use cases covered

- All 16 (regression pass)

**Roles involved:** All

## Scope

### Database

- Final index and constraint review
- Migration history is clean and reproducible from an empty database

### Backend

- `audit` module: log every read of diagnoses, prescriptions and lab results; admin can list logs
- Security review of every endpoint against `docs/project/roles-and-permissions.md`
- Database indexes reviewed against real queries; pagination enforced everywhere
- Seed script for demo data (fake doctors, patients, labs, diseases); never real data
- Production config: env validation, log levels, health checks, migration on deploy
- End-to-end test suite covering the main flows and the RBAC matrix

### Frontend

- Loading, empty and error states on every page; form validation messages
- Responsive pass (mobile, tablet, desktop) and basic accessibility pass
- GSAP polish pass: consistent timings, reduced-motion fallbacks, no animation blocking content
- Playwright end-to-end tests: auth, booking, lab result flow
- Production build check; SEO basics for public pages (titles, descriptions)

### DevOps

- Production deploy: frontend host, backend host, managed PostgreSQL, file storage, payment sandbox / gateway
- Run `prisma migrate deploy` as part of the backend release
- Custom domain / HTTPS verified; cookies verified in production
- Backup and restore of the database tested once
- Demo accounts for each role prepared and documented for the supervisor

## API endpoints

Base path `/api/v1`. Conventions: [`api-design.md`](../../architecture/api-design.md). Permissions: [`roles-and-permissions.md`](../../project/roles-and-permissions.md).

| Method | Path | Access | Use case |
| --- | --- | --- | --- |
| `GET` | `/audit-logs` | Admin | — |

## Files in this phase

Every file below already exists in the repo as a placeholder tagged with this phase. Fill them in; do not move them.

<details>
<summary><b>Repository / CI / docs</b> (1 files)</summary>

| File | Purpose |
| --- | --- |
| `.github/workflows/deploy.yml` | Release workflow: run migrations, deploy backend and frontend |

</details>

<details>
<summary><b>Backend</b> (8 files)</summary>

| File | Purpose |
| --- | --- |
| `backend/Dockerfile` | Production image for the NestJS API |
| `backend/.dockerignore` | Keep node_modules, .env and uploads out of the image |
| `backend/src/modules/audit/audit.module.ts` | Audit log - module wiring |
| `backend/src/modules/audit/audit.controller.ts` | Audit log - HTTP routes, Swagger docs, role decorators |
| `backend/src/modules/audit/audit.service.ts` | Audit log - business logic and database access |
| `backend/src/modules/audit/audit.service.spec.ts` | Audit log - unit tests for the service |
| `backend/src/modules/audit/dto/list-audit-query.dto.ts` | Filters: user, action, date range |
| `backend/test/rbac-matrix.e2e-spec.ts` | E2E: every endpoint x every role |

</details>

<details>
<summary><b>Frontend</b> (8 files)</summary>

| File | Purpose |
| --- | --- |
| `frontend/Dockerfile` | Production image for the Next.js app (optional if hosted on a platform) |
| `frontend/src/app/(dashboard)/admin/audit-logs/page.tsx` | Audit log viewer |
| `frontend/src/features/audit/components/audit-log-table.tsx` | Admin audit log table |
| `frontend/src/features/audit/api/audit.api.ts` | RTK Query endpoints for audit |
| `frontend/src/features/audit/types.ts` | TypeScript types for audit (mirror the API DTOs) |
| `frontend/e2e/auth.spec.ts` | Playwright: register, login, role redirect |
| `frontend/e2e/booking.spec.ts` | Playwright: search doctor, book, confirm |
| `frontend/e2e/lab-results.spec.ts` | Playwright: lab booking, upload, view result |

</details>

## Test scenarios

- Full regression of all 16 use cases on the deployed environment
- RBAC matrix test: every endpoint × every role returns the expected status
- Load smoke test on search and booking
- Fresh-clone test: a teammate follows the README from scratch and gets a running app

## Definition of done

- [ ] Public URL works; demo accounts for Patient, Doctor, Lab Technician and Admin
- [ ] All use cases pass; all phase checklists are ticked
- [ ] README, docs and diagrams are final; the repo has no secrets and no dead placeholders
- [ ] Presentation and demo script rehearsed

## Be ready to explain (viva / demo)

- Walk through one use case end to end, from UI to API to database
- Show the security measures: RBAC, cookies, validation, file protection, audit log
- Explain what you would build next (stretch goals) and what the MVP deliberately leaves out

## Stretch goals (not part of the MVP)

- Real-time chat / video
- Notifications
- Reviews and ratings
- Multi-language (Urdu / English)

## Vibe-coding prompt

Paste into your AI assistant after loading `AGENTS.md`. More templates: [`vibe-coding/workflow.md`](../../vibe-coding/workflow.md).

```text
Read `AGENTS.md` and `docs/phases/phase-6-integration-and-deployment/README.md` first.

Implement ONLY Integration, Testing & Deployment (Phase 6). Do not build anything from later phases.

Work in this order and stop after each step so I can test it:
1. Database: Prisma models + migration for this phase.
2. Backend: module, DTOs, service, controller, guards, unit tests, Swagger decorators.
3. Frontend: RTK Query endpoints, pages, components, forms, GSAP touches.
4. Tick the boxes in `docs/phases/phase-6-integration-and-deployment/checklist.md` for everything that works.

Do not add new features. Fix, test, secure, document, deploy.
```

---

← [Phase 5: Payments & Lab Tests](../phase-5-payments-and-lab-tests/README.md) · [Checklist](checklist.md)
