# PULSE Backend (NestJS)

REST API for PULSE. TypeScript, NestJS, Prisma, PostgreSQL.

> Scaffold only. Files are placeholders until the matching phase is built. See [`../docs/phases`](../docs/phases).

## Structure

```text
backend/
├── prisma/               # schema.prisma, migrations, seed
├── src/
│   ├── main.ts           # bootstrap
│   ├── app.module.ts
│   ├── config/           # typed config + env validation
│   ├── database/         # PrismaModule / PrismaService
│   ├── common/           # decorators, guards, filters, interceptors, dto, utils
│   └── modules/          # one folder per domain (module, controller, service, dto, spec)
└── test/                 # e2e tests
```

## Module map

| Module | Phase | Responsibility |
| --- | --- | --- |
| `health` | 0 | Liveness and database check |
| `auth` | 1 | Register, login, refresh, logout, me, change password |
| `users` | 1 | Admin: list users, approve / suspend |
| `patients` | 2 | Patient profile |
| `doctors` | 2, 3, 4 | Doctor profile, verification, search, patient-relationship rule |
| `specializations` | 2 | Specialization list and admin CRUD |
| `diseases`, `symptoms` | 2 | Disease information and symptoms |
| `schedules` | 3 | Weekly availability, time off, slot generation |
| `appointments` | 3 | Booking and appointment state machine |
| `consultations` | 4 | Diagnosis and meeting link |
| `prescriptions` | 4 | Electronic prescriptions |
| `labs` | 5 | Labs and lab-test catalogue |
| `lab-bookings` | 5 | Lab test bookings |
| `lab-results` | 5 | Result upload and protected access |
| `payments` | 5 | Payments with provider adapter (mock or gateway) |
| `storage` | 5 | File storage adapter (local or cloud) |
| `audit` | 6 | Audit log of medical-data reads |

## Conventions

- Base path `/api/v1`, Swagger at `/api/docs`.
- Global JWT guard; `@Public()` opts out; `@Roles()` restricts by role; services enforce ownership.
- DTO validation on every input; standard response envelope; stable error codes.
- Rules in full: [`../AGENTS.md`](../AGENTS.md) and [`../docs/architecture/api-design.md`](../docs/architecture/api-design.md).

## Scripts (after Phase 0)

| Command | Purpose |
| --- | --- |
| `npm run start:dev` | Run with reload |
| `npm run build` | Production build |
| `npm test` | Unit tests |
| `npm run test:e2e` | End-to-end tests |
| `npx prisma migrate dev` | Create / apply a migration locally |
| `npx prisma migrate deploy` | Apply migrations in production |
| `npx prisma db seed` | Seed data |
