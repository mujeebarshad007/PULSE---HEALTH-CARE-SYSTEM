# Phase 6 Checklist: Integration, Testing & Deployment

**Status:** ⬜ Not started · **Owner(s):** _assign_ · **Started:** _date_ · **Finished:** _date_

Plan: [README.md](README.md) · Target window: 2026-12-08 → 2026-12-16 (submission 2026-12-17, presentation 2026-12-18)

## Database

- [ ] Final index and constraint review
- [ ] Migration history is clean and reproducible from an empty database

## Backend

- [ ] `audit` module: log every read of diagnoses, prescriptions and lab results; admin can list logs
- [ ] Security review of every endpoint against `docs/project/roles-and-permissions.md`
- [ ] Database indexes reviewed against real queries; pagination enforced everywhere
- [ ] Seed script for demo data (fake doctors, patients, labs, diseases); never real data
- [ ] Production config: env validation, log levels, health checks, migration on deploy
- [ ] End-to-end test suite covering the main flows and the RBAC matrix

## Frontend

- [ ] Loading, empty and error states on every page; form validation messages
- [ ] Responsive pass (mobile, tablet, desktop) and basic accessibility pass
- [ ] GSAP polish pass: consistent timings, reduced-motion fallbacks, no animation blocking content
- [ ] Playwright end-to-end tests: auth, booking, lab result flow
- [ ] Production build check; SEO basics for public pages (titles, descriptions)

## DevOps

- [ ] Production deploy: frontend host, backend host, managed PostgreSQL, file storage, payment sandbox / gateway
- [ ] Run `prisma migrate deploy` as part of the backend release
- [ ] Custom domain / HTTPS verified; cookies verified in production
- [ ] Backup and restore of the database tested once
- [ ] Demo accounts for each role prepared and documented for the supervisor

## Endpoints working (tested in Swagger)

- [ ] `GET /audit-logs` (Admin)

## Tests

- [ ] Full regression of all 16 use cases on the deployed environment
- [ ] RBAC matrix test: every endpoint × every role returns the expected status
- [ ] Load smoke test on search and booking
- [ ] Fresh-clone test: a teammate follows the README from scratch and gets a running app

## Definition of done

- [ ] Public URL works; demo accounts for Patient, Doctor, Lab Technician and Admin
- [ ] All use cases pass; all phase checklists are ticked
- [ ] README, docs and diagrams are final; the repo has no secrets and no dead placeholders
- [ ] Presentation and demo script rehearsed

## Viva / demo preparation

- [ ] Every member can explain: Walk through one use case end to end, from UI to API to database
- [ ] Every member can explain: Show the security measures: RBAC, cookies, validation, file protection, audit log
- [ ] Every member can explain: Explain what you would build next (stretch goals) and what the MVP deliberately leaves out

## Sign-off

- [ ] Reviewed by another team member: ______
- [ ] `develop` merged into `main`
- [ ] Tagged `v0.6.0`
- [ ] `CHANGELOG.md` updated
