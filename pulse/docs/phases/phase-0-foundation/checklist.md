# Phase 0 Checklist: Foundation & Setup

**Status:** ⬜ Not started · **Owner(s):** _assign_ · **Started:** _date_ · **Finished:** _date_

Plan: [README.md](README.md) · Target window: Now → 2026-11-06 (repo and tooling ideally by 2026-10-02; schema and UI design by 2026-11-06)

## Database

- [ ] PostgreSQL runs locally through Docker Compose
- [ ] No business tables yet: every phase ships its own migration (matches the incremental model)

## Backend

- [ ] Initialise the NestJS project (TypeScript, strict mode) in `backend/`
- [ ] Config module with typed environment validation (app refuses to start on missing variables)
- [ ] Prisma set up and connected to PostgreSQL; `PrismaModule` / `PrismaService`
- [ ] Global validation pipe (whitelist + transform), global exception filters, response-envelope interceptor
- [ ] Security baseline: Helmet, CORS locked to the frontend origin with credentials, rate limiting, cookie parser
- [ ] API prefix `/api/v1` and Swagger UI at `/api/docs`
- [ ] Health module: `GET /health` that also checks database connectivity
- [ ] Create the baseline `schema.prisma` (datasource + generator only; tables arrive phase by phase)

## Frontend

- [ ] Initialise Next.js (App Router, TypeScript, Tailwind, `src/` directory) in `frontend/`
- [ ] Initialise shadcn/ui and add the base components (button, input, form, card, dialog, dropdown, table, select, badge, skeleton, tabs, sonner/toast)
- [ ] Design tokens: healthcare colour palette (calm blues/teals), typography, radius, spacing; light theme first
- [ ] Redux Toolkit store + RTK Query base API (base URL `/api`, credentials included)
- [ ] GSAP installed; plugins registered in one place (`lib/gsap.ts`); reusable animation components; respects reduced-motion
- [ ] Layout shell: navbar, footer, landing-page skeleton, 404 / error / loading pages
- [ ] Next.js rewrites: proxy `/api/*` to the backend so cookies stay first-party

## DevOps

- [ ] Repo hygiene: `.gitignore`, `.editorconfig`, PR template, issue templates, protected `main` branch
- [ ] GitHub Actions: lint + build + test for frontend and backend
- [ ] Deploy an empty staging environment: frontend host + backend host + managed PostgreSQL
- [ ] Prove the round trip: deployed frontend calls deployed API, which reads the deployed database (health check)

## Endpoints working (tested in Swagger)

- [ ] `GET /health` (Public)

## Tests

- [ ] `GET /health` returns OK and reports DB status
- [ ] Backend refuses to boot when a required env var is missing
- [ ] Frontend builds and renders the landing skeleton
- [ ] CI is green on a pull request

## Definition of done

- [ ] `docker compose up` gives a working local database
- [ ] Backend and frontend both run locally with one documented command each
- [ ] Staging URL loads the frontend and the health round trip works
- [ ] CI passes; `AGENTS.md` and `CONTRIBUTING.md` are agreed by all three members

## Viva / demo preparation

- [ ] Every member can explain: Why a monorepo with two apps (one repo, one issue board, one CI, easy for a 3-person team)
- [ ] Every member can explain: Why migrations per phase (each increment is testable and shippable on its own)
- [ ] Every member can explain: Why the `/api` proxy (first-party cookies, no CORS pain in production)

## Sign-off

- [ ] Reviewed by another team member: ______
- [ ] `develop` merged into `main`
- [ ] Tagged `v0.0.0`
- [ ] `CHANGELOG.md` updated
