# PULSE Frontend (Next.js)

Web app for PULSE. Next.js (App Router), TypeScript, Redux Toolkit + RTK Query, shadcn/ui, Tailwind CSS, GSAP.

> Scaffold only. Files are placeholders until the matching phase is built. See [`../docs/phases`](../docs/phases).

## Structure

```text
frontend/
├── public/
├── e2e/                      # Playwright tests (Phase 6)
└── src/
    ├── app/
    │   ├── (public)/         # doctors, diseases, labs (no login needed)
    │   ├── (auth)/           # login, register, pending-verification
    │   └── (dashboard)/      # protected: patient/, doctor/, lab/, admin/
    ├── components/
    │   ├── ui/               # shadcn/ui (generated)
    │   ├── layout/           # navbar, footer, sidebar, headers
    │   ├── common/           # data table, empty state, status badge, upload, ...
    │   ├── animations/       # GSAP wrappers
    │   └── providers/        # Redux, theme
    ├── features/<domain>/    # components/, api/ (RTK Query), schemas.ts, types.ts
    ├── store/                # Redux store + RTK Query base API
    ├── lib/  hooks/  types/  config/
    └── middleware.ts         # route protection (proxy.ts on Next.js 16+)
```

## Rules of thumb

- Server data only through RTK Query in `features/<domain>/api`.
- UI from shadcn/ui + Tailwind utilities; do not hand-edit `components/ui`.
- Forms: React Hook Form + Zod (`schemas.ts` per feature).
- GSAP only in `components/animations` and small hooks; clean up and respect reduced motion.
- Every page handles loading, empty and error states.
- Auth is httpOnly cookies. Nothing sensitive in `localStorage`.
- Hiding UI by role is UX only; the API enforces permissions.

Full rules: [`../AGENTS.md`](../AGENTS.md).

## Scripts (after Phase 0)

| Command | Purpose |
| --- | --- |
| `npm run dev` | Dev server on port 3000 |
| `npm run build` | Production build |
| `npm run lint` | Lint |
| `npx playwright test` | End-to-end tests |
| `npx shadcn@latest add <component>` | Add a shadcn/ui component |
