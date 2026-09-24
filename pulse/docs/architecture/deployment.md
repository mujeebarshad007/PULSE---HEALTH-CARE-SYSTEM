# Deployment

Goal: a public URL for the final demo, reproducible from the repo.

## Environments

| Environment | Purpose | Database |
| --- | --- | --- |
| Local | Development | Docker PostgreSQL |
| Staging (from Phase 0) | Team testing, supervisor previews | Managed PostgreSQL |
| Production (Phase 6) | Final demo | Managed PostgreSQL (separate from staging) |

## Hosting options

Free tiers, limits and sign-up requirements (for example a payment card) change often. **Verify current terms before choosing**, and pick something every team member can access.

| Part | Options to evaluate |
| --- | --- |
| Frontend (Next.js) | A Next.js-friendly platform (for example Vercel), or Docker on a VPS / container host |
| Backend (NestJS) | A web-service / container host (for example Render, Railway, Fly.io), or Docker on a VPS |
| PostgreSQL | A managed provider (for example Neon, Supabase, Railway, Render) |
| File storage | S3-compatible object storage (for example AWS S3, Cloudflare R2, Supabase Storage) |
| Payments | Pick the gateway in Phase 5. Check which providers offer a sandbox you can actually register for (Pakistani providers such as JazzCash or Easypaisa, or international test-mode providers). Until then, use the mock provider. |

Some free hosts put idle services to sleep. Open the app a few minutes before the demo.

## Topology

- The browser talks only to the frontend domain.
- The frontend server proxies `/api/*` to the backend (`API_PROXY_TARGET`).
- The backend talks to PostgreSQL and file storage over private credentials.

## Release steps (backend)

1. CI passes on `main`.
2. Build the backend.
3. Run `prisma migrate deploy` against the target database.
4. Start the service; the platform checks `GET /health`.
5. Smoke test: log in as the demo admin.

## Release steps (frontend)

1. CI passes on `main`.
2. Set `API_PROXY_TARGET` to the backend URL.
3. Build and deploy.
4. Smoke test: landing page, doctor search, login.

## Configuration checklist

- [ ] `NODE_ENV=production`, `COOKIE_SECURE=true`
- [ ] Unique, long JWT secrets
- [ ] `FRONTEND_URL` matches the deployed frontend origin
- [ ] `DATABASE_URL` points to the managed database
- [ ] `STORAGE_DRIVER=cloud` and credentials set (local disk is not persistent on most hosts)
- [ ] `PAYMENT_PROVIDER` and webhook secret set; webhook URL registered with the gateway
- [ ] `APP_TIMEZONE` set
- [ ] Seed admin created, password changed

## Docker (fallback / self-hosting)

`backend/Dockerfile` and `frontend/Dockerfile` (Phase 6) allow running everything on a single VPS with Docker Compose plus a reverse proxy with HTTPS.

## Backups

Enable the provider's automated backups if available, and **restore one backup once** before the demo to prove it works.

## Pre-demo checklist

- [ ] Public URL loads on phone and laptop
- [ ] Demo accounts for all four roles work
- [ ] Demo data present (doctors, diseases, labs, a sample lab result)
- [ ] Payment flow works (mock or sandbox)
- [ ] Service wake-up done
- [ ] Backup plan: local run + screen recording
