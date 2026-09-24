# AGENTS.md

Instructions for AI coding assistants (Claude Code, Cursor, Copilot, etc.) working in this repository. Humans: read it too, it is the shortest summary of how we build PULSE.

## Project in one paragraph

PULSE is a web-based healthcare information and appointment system: patients search doctors and book appointments, doctors consult and prescribe, lab technicians upload results, admins manage the platform. Monorepo: `frontend/` (Next.js) and `backend/` (NestJS) with PostgreSQL. It is a university project with a fixed deadline, so **simple, readable and explainable beats clever**.

## Sources of truth (read before coding)

| Need | File |
| --- | --- |
| What to build right now | `docs/phases/<current-phase>/README.md` |
| What is in scope | `docs/project/use-case-matrix.md` |
| Who can do what | `docs/project/roles-and-permissions.md` |
| Tables and relations | `docs/architecture/database-design.md` |
| Endpoints and conventions | `docs/architecture/api-design.md` |
| Security rules | `docs/architecture/auth-and-security.md` |
| Where files go | `docs/project/repository-structure.md` |

If a doc and your assumption disagree, the doc wins. If the doc is silent or wrong, **ask the human** instead of guessing, and propose a doc update.

## How to work

1. **One phase at a time.** Never implement features of later phases, even "quickly". Each file in the repo is tagged with its phase in its placeholder header.
2. **Vertical slices, in this order:** database (Prisma model + migration) → backend (DTOs, service, controller, tests) → frontend (RTK Query, pages, components). Stop after each step so the human can run and review.
3. **Fill the existing placeholder files** at the paths already in the repo. Do not invent a different structure. If you need a new file, put it where the structure says it belongs and mention it.
4. **Keep it boring.** No new libraries, patterns or abstractions without asking. Prefer plain functions and small files. Add short comments only where the reason is not obvious.
5. **Finish properly:** run lint, type-check and tests; tick the boxes in the phase `checklist.md`; add a line to `CHANGELOG.md`.
6. **Explain on request.** The team must be able to defend every line in a viva. When asked, explain code in plain language.

## Backend rules (NestJS)

- Every route is protected by default (global `JwtAuthGuard`). Use `@Public()` only for genuinely public routes. Use `@Roles(...)` for role limits.
- Roles are not enough: services must also check **ownership / relationship** (see roles-and-permissions.md). Never trust ids or roles sent by the client.
- Validate all input with DTOs (`class-validator`), `whitelist` and `forbidNonWhitelisted` on.
- Controllers stay thin (routing, decorators, Swagger). Business rules live in services.
- All responses use the standard envelope; errors use stable error codes from `api-design.md`.
- Never return `passwordHash`, refresh tokens, or storage keys. Use explicit response shapes.
- Booking and payment updates run inside transactions. Double-booking is prevented by a database constraint, not just by code.
- Prisma only through `PrismaService`. One migration per meaningful change; never edit an applied migration.
- Log without sensitive bodies (no passwords, tokens, medical text).
- Every service gets a unit test for its business rules; every phase extends the e2e tests.

## Frontend rules (Next.js)

- **All server data goes through RTK Query** (`features/<domain>/api`). No `fetch` / `axios` calls inside components.
- **UI uses shadcn/ui components** from `components/ui` and Tailwind utility classes. Do not hand-edit generated shadcn files; wrap them instead.
- Forms: React Hook Form + Zod schemas in `features/<domain>/schemas.ts`.
- **GSAP** lives in `components/animations` and small hooks. Always clean up (`gsap.context` / `revert`), respect `prefers-reduced-motion`, and never let animation hide or delay important content.
- Every list/page handles **loading, empty and error** states.
- Server components by default; add `"use client"` only where state or effects are needed.
- Role-based hiding of UI is UX only. The API is the real security.
- Route protection file: `middleware.ts` (named `proxy.ts` on Next.js 16+; check the installed version).
- Never store tokens in `localStorage` / `sessionStorage`. Auth is httpOnly cookies.

## Security rules (non-negotiable)

- No secrets in git. Only `.env.example` files are committed.
- Lab report files are private: random storage keys, allow-listed types, size limit, served only via authenticated endpoints.
- Admin cannot read diagnoses, prescriptions or lab files.
- A doctor can read a patient's history / lab results only with a CONFIRMED or COMPLETED appointment relationship.
- Payments: never handle card data; verify gateway callbacks.
- Use fake data only, everywhere.

## Conventions

- TypeScript strict. No `any` unless commented why.
- Files: `kebab-case`. React components: `PascalCase` exports. DTOs: `*.dto.ts`. RTK Query files: `*.api.ts`.
- API base path `/api/v1`, plural nouns, ids are UUIDs.
- Dates stored in UTC; schedule logic uses the single configured `APP_TIMEZONE`.
- Commits: Conventional Commits (`feat(auth): ...`). Branches: see `CONTRIBUTING.md`.

## Placeholders

Files in the scaffold start with a comment header: `PLACEHOLDER - no code yet`, the phase, the purpose. Replace the header with real code when you implement the file. Files that stay unused in a phase stay untouched.

## Bootstrapping (Phase 0 only)

`create-next-app` and `nest new` refuse non-empty folders. Run them in a scratch folder and copy the generated config files (`package.json`, tsconfig, framework config, lint config) into `frontend/` / `backend/` without overwriting the `src/` placeholders. Keep the folder structure exactly as it is.

## Definition of done (per task)

- Works locally and matches the phase README.
- Lint, type-check and tests pass.
- Permissions verified for every role that touches it.
- Checklist ticked, `CHANGELOG.md` updated.
- Human has reviewed the diff.
