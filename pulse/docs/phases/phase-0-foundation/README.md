# Phase 0: Foundation & Setup

> **Increment:** Setup · **Target window:** Now → 2026-11-06 (repo and tooling ideally by 2026-10-02; schema and UI design by 2026-11-06) · **Status:** ⬜ Not started

> This document is a plan. It contains **no application code**.

## Goal

A deployed 'walking skeleton': frontend, backend and database talking to each other on a live URL, with CI, conventions and design tokens locked in. No business features yet.

## Use cases covered

None. This phase builds the foundation the use cases stand on.

**Roles involved:** —

## Scope

### Database

- PostgreSQL runs locally through Docker Compose
- No business tables yet: every phase ships its own migration (matches the incremental model)

### Backend

- Initialise the NestJS project (TypeScript, strict mode) in `backend/`
- Config module with typed environment validation (app refuses to start on missing variables)
- Prisma set up and connected to PostgreSQL; `PrismaModule` / `PrismaService`
- Global validation pipe (whitelist + transform), global exception filters, response-envelope interceptor
- Security baseline: Helmet, CORS locked to the frontend origin with credentials, rate limiting, cookie parser
- API prefix `/api/v1` and Swagger UI at `/api/docs`
- Health module: `GET /health` that also checks database connectivity
- Create the baseline `schema.prisma` (datasource + generator only; tables arrive phase by phase)

### Frontend

- Initialise Next.js (App Router, TypeScript, Tailwind, `src/` directory) in `frontend/`
- Initialise shadcn/ui and add the base components (button, input, form, card, dialog, dropdown, table, select, badge, skeleton, tabs, sonner/toast)
- Design tokens: healthcare colour palette (calm blues/teals), typography, radius, spacing; light theme first
- Redux Toolkit store + RTK Query base API (base URL `/api`, credentials included)
- GSAP installed; plugins registered in one place (`lib/gsap.ts`); reusable animation components; respects reduced-motion
- Layout shell: navbar, footer, landing-page skeleton, 404 / error / loading pages
- Next.js rewrites: proxy `/api/*` to the backend so cookies stay first-party

### DevOps

- Repo hygiene: `.gitignore`, `.editorconfig`, PR template, issue templates, protected `main` branch
- GitHub Actions: lint + build + test for frontend and backend
- Deploy an empty staging environment: frontend host + backend host + managed PostgreSQL
- Prove the round trip: deployed frontend calls deployed API, which reads the deployed database (health check)

## API endpoints

Base path `/api/v1`. Conventions: [`api-design.md`](../../architecture/api-design.md). Permissions: [`roles-and-permissions.md`](../../project/roles-and-permissions.md).

| Method | Path | Access | Use case |
| --- | --- | --- | --- |
| `GET` | `/health` | Public | — |

## Files in this phase

Every file below already exists in the repo as a placeholder tagged with this phase. Fill them in; do not move them.

<details>
<summary><b>Repository / CI / docs</b> (11 files)</summary>

| File | Purpose |
| --- | --- |
| `.gitignore` | Ignore node_modules, build output, .env files, uploads, OS/editor files |
| `.editorconfig` | Shared editor settings (indent, line endings, charset) |
| `.env.example` | Variables read by docker-compose (database name, user, password) |
| `docker-compose.yml` | Local PostgreSQL (and optional pgAdmin); backend + frontend containers added in Phase 6 |
| `LICENSE` | Licence text (team decides before the repo goes public) |
| `CHANGELOG.md` | Human-readable history: one entry per phase / release |
| `.github/pull_request_template.md` | PR checklist: phase, use case, tests, screenshots, docs updated |
| `.github/ISSUE_TEMPLATE/feature_request.md` | Template for a feature / task issue linked to a use case |
| `.github/ISSUE_TEMPLATE/bug_report.md` | Template for a bug report with steps, expected, actual |
| `.github/workflows/ci-backend.yml` | CI: install, lint, unit + e2e tests, build for the backend |
| `.github/workflows/ci-frontend.yml` | CI: install, lint, type-check, build for the frontend |

</details>

<details>
<summary><b>Backend</b> (18 files)</summary>

| File | Purpose |
| --- | --- |
| `backend/.env.example` | Backend environment variables (database, JWT secrets, storage, payments, seed admin) |
| `backend/prisma/schema.prisma` | Datasource + generator in Phase 0; models are added phase by phase (see docs/architecture/database-design.md) |
| `backend/src/main.ts` | App bootstrap: prefix, validation pipe, Helmet, CORS, cookies, Swagger |
| `backend/src/app.module.ts` | Root module wiring every feature module |
| `backend/src/config/configuration.ts` | Typed config object built from environment variables |
| `backend/src/config/env.validation.ts` | Validates environment variables on startup (fail fast) |
| `backend/src/database/prisma.module.ts` | Global module exposing PrismaService |
| `backend/src/database/prisma.service.ts` | Prisma client lifecycle (connect / disconnect) |
| `backend/src/common/filters/http-exception.filter.ts` | Uniform error response shape with stable error codes |
| `backend/src/common/filters/prisma-exception.filter.ts` | Map Prisma errors (unique, not found) to clean HTTP errors |
| `backend/src/common/interceptors/response-envelope.interceptor.ts` | Wrap successful responses in the standard envelope |
| `backend/src/common/interceptors/logging.interceptor.ts` | Request logging (method, path, status, duration; no sensitive bodies) |
| `backend/src/common/dto/pagination-query.dto.ts` | Shared page / limit / sort query parameters |
| `backend/src/common/dto/paginated-response.dto.ts` | Shared shape for paginated lists |
| `backend/src/common/utils/pagination.util.ts` | Helpers to turn page / limit into skip / take and build meta |
| `backend/src/modules/health/health.module.ts` | Health check module |
| `backend/src/modules/health/health.controller.ts` | GET /health including database ping |
| `backend/test/app.e2e-spec.ts` | E2E: health check |

</details>

<details>
<summary><b>Frontend</b> (31 files)</summary>

| File | Purpose |
| --- | --- |
| `frontend/.env.example` | Frontend environment variables (API proxy target, public app name) |
| `frontend/src/app/layout.tsx` | Root layout: fonts, providers (Redux, theme, toaster) |
| `frontend/src/app/globals.css` | Tailwind import and design tokens (shadcn CSS variables) |
| `frontend/src/app/page.tsx` | Landing page: hero, how it works, popular specializations (GSAP intro) |
| `frontend/src/app/not-found.tsx` | 404 page |
| `frontend/src/app/error.tsx` | Global error boundary page |
| `frontend/src/app/loading.tsx` | Global loading state |
| `frontend/src/app/(public)/layout.tsx` | Public layout: navbar + footer |
| `frontend/src/components/layout/navbar.tsx` | Public navbar |
| `frontend/src/components/layout/footer.tsx` | Public footer |
| `frontend/src/components/common/page-header.tsx` | Title + description + action slot |
| `frontend/src/components/common/empty-state.tsx` | Empty list placeholder |
| `frontend/src/components/common/loading-skeletons.tsx` | Skeleton loaders |
| `frontend/src/components/common/error-message.tsx` | Inline error display |
| `frontend/src/components/animations/fade-in.tsx` | Reusable GSAP fade / slide-in wrapper |
| `frontend/src/components/animations/stagger-children.tsx` | Reusable GSAP stagger for lists and grids |
| `frontend/src/components/animations/page-transition.tsx` | Route-change transition wrapper |
| `frontend/src/components/animations/count-up.tsx` | Animated number counter for dashboards |
| `frontend/src/components/providers/store-provider.tsx` | Redux Provider (client component) |
| `frontend/src/components/providers/theme-provider.tsx` | Theme provider |
| `frontend/src/store/index.ts` | Configure the Redux store |
| `frontend/src/store/hooks.ts` | Typed useAppDispatch / useAppSelector |
| `frontend/src/store/base-api.ts` | RTK Query base API: /api base URL, credentials, refresh-on-401 (P1) |
| `frontend/src/lib/utils.ts` | cn() class merge helper (created by shadcn) and small utilities |
| `frontend/src/lib/gsap.ts` | Register GSAP plugins once; export the configured gsap |
| `frontend/src/lib/constants.ts` | App-wide constants |
| `frontend/src/hooks/use-reduced-motion.ts` | Detect prefers-reduced-motion so GSAP can skip animation |
| `frontend/src/types/api.ts` | Standard response envelope, pagination and error types |
| `frontend/src/types/index.ts` | Barrel export |
| `frontend/src/config/env.ts` | Read and validate public env vars |
| `frontend/src/config/routes.ts` | Route path constants |

</details>

## Test scenarios

- `GET /health` returns OK and reports DB status
- Backend refuses to boot when a required env var is missing
- Frontend builds and renders the landing skeleton
- CI is green on a pull request

## Definition of done

- [ ] `docker compose up` gives a working local database
- [ ] Backend and frontend both run locally with one documented command each
- [ ] Staging URL loads the frontend and the health round trip works
- [ ] CI passes; `AGENTS.md` and `CONTRIBUTING.md` are agreed by all three members

## Be ready to explain (viva / demo)

- Why a monorepo with two apps (one repo, one issue board, one CI, easy for a 3-person team)
- Why migrations per phase (each increment is testable and shippable on its own)
- Why the `/api` proxy (first-party cookies, no CORS pain in production)

## Stretch goals (not part of the MVP)

- Dark mode
- Sentry-style error tracking

## Vibe-coding prompt

Paste into your AI assistant after loading `AGENTS.md`. More templates: [`vibe-coding/workflow.md`](../../vibe-coding/workflow.md).

```text
Read `AGENTS.md` and `docs/phases/phase-0-foundation/README.md` first.

Implement ONLY Foundation & Setup (Phase 0). Do not build anything from later phases.

Work in this order and stop after each step so I can test it:
1. Database: Prisma models + migration for this phase.
2. Backend: module, DTOs, service, controller, guards, unit tests, Swagger decorators.
3. Frontend: RTK Query endpoints, pages, components, forms, GSAP touches.
4. Tick the boxes in `docs/phases/phase-0-foundation/checklist.md` for everything that works.

Also set up the CI workflows and Docker Compose. Do not add any business tables.
```

---

[Phase 1: Authentication & User Accounts](../phase-1-authentication/README.md) → · [Checklist](checklist.md)
