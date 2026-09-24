# Phase 5: Payments & Lab Tests

> **Increment:** Increment 5 (Sprint 4) · **Target window:** 2026-12-03 → 2026-12-07 · **Status:** ⬜ Not started

> This document is a plan. It contains **no application code**.

## Goal

Patients search lab tests, book them, and pay online for appointments and lab tests; lab technicians confirm bookings and upload results; patients (and their doctors) view results online.

## Use cases covered

- UC-07
- UC-08 (lab side)
- UC-11
- UC-12
- UC-13

**Roles involved:** Patient, Doctor, Lab Technician, Admin

## Decisions made for this phase

- Admin manages labs, the test catalogue and technician assignment (not in the use case diagram; confirm with the team).
- Payment gateway is not decided in the plan. The adapter design lets you start with the mock and swap later.

Change these here first if the team disagrees, then update the checklist.

## Scope

### Database

- Enums: `LabBookingStatus` (PENDING, CONFIRMED, COMPLETED, CANCELLED), `PaymentStatus` (PENDING, PAID, FAILED, REFUNDED), `PaymentPurpose` (APPOINTMENT, LAB_BOOKING)
- Models: `Lab`, `LabTest` (price, description, preparation notes), `LabTechnicianProfile` (user → lab), `LabBooking`, `LabResult` (file key, mime type, size, summary, uploadedBy), `Payment` (purpose, amount, currency, provider, providerRef, status, paidAt)

### Backend

- `labs` module: labs and test catalogue; public search; admin manages labs, tests and technician assignment
- `lab-bookings` module: patient books a test; lab technician confirms, completes or cancels
- `lab-results` module: technician uploads a report file; patient and related doctor read it
- `storage` module: provider interface with a local-disk driver (dev) and a cloud-storage driver (production); files are never public
- `payments` module: create payment for an appointment or a lab booking, gateway callback, status, history
- Payment provider adapter: mock provider for development and demos, real gateway plugged in behind the same interface
- Doctor endpoint for a patient's lab results (needs the Phase 4 relationship rule)

### Frontend

- Lab and lab-test search pages, lab detail with its tests
- Lab booking flow for patients; patient lab bookings and lab results pages with a result viewer
- Lab technician dashboard: booking queue, confirm, upload result
- Admin: labs and lab-test catalogue management, technician assignment
- Checkout panel, payment status page, payment history
- Doctor patient-history page gains a lab results section
- GSAP: checkout progress, upload progress, result-ready reveal

### DevOps

- Configure cloud file storage and payment sandbox credentials in staging

## API endpoints

Base path `/api/v1`. Conventions: [`api-design.md`](../../architecture/api-design.md). Permissions: [`roles-and-permissions.md`](../../project/roles-and-permissions.md).

| Method | Path | Access | Use case |
| --- | --- | --- | --- |
| `GET` | `/labs` | Public | UC-07 |
| `GET` | `/labs/:id` | Public | UC-07 |
| `POST` | `/labs` | Admin | UC-07 |
| `PATCH` | `/labs/:id` | Admin | UC-07 |
| `PATCH` | `/labs/:id/technicians/:userId` | Admin | UC-07 |
| `GET` | `/lab-tests` | Public | UC-07 |
| `POST` | `/labs/:id/tests` | Admin | UC-07 |
| `PATCH` | `/lab-tests/:id` | Admin | UC-07 |
| `DELETE` | `/lab-tests/:id` | Admin | UC-07 |
| `POST` | `/lab-bookings` | Patient | UC-07 |
| `GET` | `/lab-bookings` | Patient (own) / Lab Technician (own lab) | UC-08 |
| `GET` | `/lab-bookings/:id` | Patient (own) / Lab Technician (own lab) | UC-08 |
| `PATCH` | `/lab-bookings/:id/confirm` | Lab Technician | UC-08 |
| `PATCH` | `/lab-bookings/:id/cancel` | Patient / Lab Technician | UC-08 |
| `POST` | `/lab-bookings/:id/result` | Lab Technician (multipart upload) | UC-13 |
| `GET` | `/lab-results` | Patient (own) / Lab Technician (own lab) | UC-12 |
| `GET` | `/lab-results/:id` | Patient (own) / Doctor (with relationship) / Lab Technician (own lab) | UC-12 |
| `GET` | `/lab-results/:id/file` | Same as above; streams the file | UC-12 |
| `GET` | `/doctors/me/patients/:patientId/lab-results` | Doctor (with relationship) | UC-12 |
| `POST` | `/payments` | Patient | UC-11 |
| `GET` | `/payments` | Patient (own) / Admin (all, metadata) | UC-11 |
| `GET` | `/payments/:id` | Patient (own) / Admin | UC-11 |
| `POST` | `/payments/webhook` | Gateway (signature verified) | UC-11 |

## Files in this phase

Every file below already exists in the repo as a placeholder tagged with this phase. Fill them in; do not move them.

<details>
<summary><b>Backend</b> (36 files)</summary>

| File | Purpose |
| --- | --- |
| `backend/src/common/utils/file-validation.util.ts` | Allowed mime types and size limit for uploads |
| `backend/src/modules/labs/labs.module.ts` | Labs and test catalogue - module wiring |
| `backend/src/modules/labs/labs.controller.ts` | Labs and test catalogue - HTTP routes, Swagger docs, role decorators |
| `backend/src/modules/labs/labs.service.ts` | Labs and test catalogue - business logic and database access |
| `backend/src/modules/labs/labs.service.spec.ts` | Labs and test catalogue - unit tests for the service |
| `backend/src/modules/labs/dto/create-lab.dto.ts` | Create lab |
| `backend/src/modules/labs/dto/update-lab.dto.ts` | Update lab |
| `backend/src/modules/labs/dto/create-lab-test.dto.ts` | Create test in a lab |
| `backend/src/modules/labs/dto/update-lab-test.dto.ts` | Update test |
| `backend/src/modules/labs/dto/search-lab-tests-query.dto.ts` | Search by name, lab, city |
| `backend/src/modules/lab-bookings/lab-bookings.module.ts` | Lab test bookings - module wiring |
| `backend/src/modules/lab-bookings/lab-bookings.controller.ts` | Lab test bookings - HTTP routes, Swagger docs, role decorators |
| `backend/src/modules/lab-bookings/lab-bookings.service.ts` | Lab test bookings - business logic and database access |
| `backend/src/modules/lab-bookings/lab-bookings.service.spec.ts` | Lab test bookings - unit tests for the service |
| `backend/src/modules/lab-bookings/dto/create-lab-booking.dto.ts` | Test id, preferred date / time |
| `backend/src/modules/lab-bookings/dto/update-lab-booking-status.dto.ts` | Confirm / cancel with reason |
| `backend/src/modules/lab-results/lab-results.module.ts` | Lab results - module wiring |
| `backend/src/modules/lab-results/lab-results.controller.ts` | Lab results - HTTP routes, Swagger docs, role decorators |
| `backend/src/modules/lab-results/lab-results.service.ts` | Lab results - business logic and database access |
| `backend/src/modules/lab-results/lab-results.service.spec.ts` | Lab results - unit tests for the service |
| `backend/src/modules/lab-results/dto/upload-lab-result.dto.ts` | Summary text accompanying the uploaded file |
| `backend/src/modules/payments/payments.module.ts` | Payments - module wiring |
| `backend/src/modules/payments/payments.controller.ts` | Payments - HTTP routes, Swagger docs, role decorators |
| `backend/src/modules/payments/payments.service.ts` | Payments - business logic and database access |
| `backend/src/modules/payments/payments.service.spec.ts` | Payments - unit tests for the service |
| `backend/src/modules/payments/dto/create-payment.dto.ts` | Purpose + target id (appointment or lab booking) |
| `backend/src/modules/payments/providers/payment-provider.interface.ts` | Contract every payment provider implements |
| `backend/src/modules/payments/providers/mock-payment.provider.ts` | Fake gateway for development and demos |
| `backend/src/modules/payments/providers/gateway-payment.provider.ts` | Real / sandbox gateway implementation (choose with the team) |
| `backend/src/modules/storage/storage.module.ts` | File storage abstraction - module wiring |
| `backend/src/modules/storage/storage.controller.ts` | File storage abstraction - HTTP routes, Swagger docs, role decorators |
| `backend/src/modules/storage/storage.service.ts` | File storage abstraction - business logic and database access |
| `backend/src/modules/storage/providers/storage-provider.interface.ts` | Contract: save, read stream, delete |
| `backend/src/modules/storage/providers/local-storage.provider.ts` | Local disk driver for development |
| `backend/src/modules/storage/providers/cloud-storage.provider.ts` | Cloud object-storage driver for production |
| `backend/test/labs-and-payments.e2e-spec.ts` | E2E: lab booking, result upload / access, payment flow |

</details>

<details>
<summary><b>Frontend</b> (39 files)</summary>

| File | Purpose |
| --- | --- |
| `frontend/src/app/(public)/labs/page.tsx` | Lab and lab-test search |
| `frontend/src/app/(public)/labs/[labId]/page.tsx` | Lab detail with its tests |
| `frontend/src/app/(dashboard)/patient/lab-bookings/page.tsx` | My lab test bookings |
| `frontend/src/app/(dashboard)/patient/lab-results/page.tsx` | My lab results |
| `frontend/src/app/(dashboard)/patient/lab-results/[resultId]/page.tsx` | Result viewer / download |
| `frontend/src/app/(dashboard)/patient/payments/page.tsx` | Payment history |
| `frontend/src/app/(dashboard)/patient/payments/[paymentId]/page.tsx` | Checkout status / receipt |
| `frontend/src/app/(dashboard)/lab/page.tsx` | Lab technician dashboard |
| `frontend/src/app/(dashboard)/lab/bookings/page.tsx` | Booking queue |
| `frontend/src/app/(dashboard)/lab/bookings/[bookingId]/page.tsx` | Booking detail and result upload |
| `frontend/src/app/(dashboard)/lab/results/page.tsx` | Results I uploaded |
| `frontend/src/app/(dashboard)/admin/labs/page.tsx` | Manage labs |
| `frontend/src/app/(dashboard)/admin/labs/[labId]/page.tsx` | Lab tests catalogue and technician assignment |
| `frontend/src/components/common/file-upload.tsx` | File picker with type / size checks and progress |
| `frontend/src/features/labs/components/lab-card.tsx` | Lab summary card |
| `frontend/src/features/labs/components/lab-test-search.tsx` | Search lab tests |
| `frontend/src/features/labs/components/lab-test-list.tsx` | Tests of a lab |
| `frontend/src/features/labs/components/lab-form.tsx` | Admin lab form |
| `frontend/src/features/labs/components/lab-test-form.tsx` | Admin test form |
| `frontend/src/features/labs/api/labs.api.ts` | RTK Query endpoints for labs |
| `frontend/src/features/labs/types.ts` | TypeScript types for labs (mirror the API DTOs) |
| `frontend/src/features/labs/schemas.ts` | Zod schemas for lab / test forms |
| `frontend/src/features/lab-bookings/components/lab-booking-form.tsx` | Book a lab test |
| `frontend/src/features/lab-bookings/components/lab-booking-list.tsx` | Bookings list |
| `frontend/src/features/lab-bookings/components/lab-booking-status-actions.tsx` | Confirm / cancel actions |
| `frontend/src/features/lab-bookings/api/lab-bookings.api.ts` | RTK Query endpoints for lab-bookings |
| `frontend/src/features/lab-bookings/types.ts` | TypeScript types for lab-bookings (mirror the API DTOs) |
| `frontend/src/features/lab-bookings/schemas.ts` | Zod schema for lab booking |
| `frontend/src/features/lab-results/components/lab-result-upload-form.tsx` | Technician upload form |
| `frontend/src/features/lab-results/components/lab-result-card.tsx` | Result summary card |
| `frontend/src/features/lab-results/components/lab-result-viewer.tsx` | Secure viewer / download |
| `frontend/src/features/lab-results/api/lab-results.api.ts` | RTK Query endpoints for lab-results |
| `frontend/src/features/lab-results/types.ts` | TypeScript types for lab-results (mirror the API DTOs) |
| `frontend/src/features/lab-results/schemas.ts` | Zod schema for result upload |
| `frontend/src/features/payments/components/checkout-panel.tsx` | Start payment for an appointment or lab booking |
| `frontend/src/features/payments/components/payment-history-table.tsx` | Payment history |
| `frontend/src/features/payments/components/payment-status-badge.tsx` | Payment status pill |
| `frontend/src/features/payments/api/payments.api.ts` | RTK Query endpoints for payments |
| `frontend/src/features/payments/types.ts` | TypeScript types for payments (mirror the API DTOs) |

</details>

## Test scenarios

- Lab technician can only touch bookings of their own lab
- Upload rejects wrong file types and oversize files; stored under a random key
- Result files are only reachable through the authenticated endpoint (no public URL)
- A patient cannot read another patient's result; a doctor without a relationship cannot either
- Payment webhook with a bad signature is rejected; paying twice does not double-charge
- Online appointment: meeting link stays hidden until payment is PAID

## Definition of done

- [ ] End to end: search test → book → lab confirms → lab uploads result → patient views it
- [ ] End to end: confirmed online appointment → pay → meeting link released
- [ ] Payments run on the mock provider locally and on the sandbox gateway in staging
- [ ] UC-07, UC-08 (lab side), UC-11, UC-12, UC-13 pass their acceptance check

## Be ready to explain (viva / demo)

- Why a payment provider adapter (gateway availability is a plan constraint; the rest of the app does not care which one is used)
- Why the app never stores card data and verifies the gateway callback
- Why lab reports are served through an authenticated endpoint instead of public links

## Stretch goals (not part of the MVP)

- Refund flow
- Payment receipts as PDF
- Result-ready notifications

## Vibe-coding prompt

Paste into your AI assistant after loading `AGENTS.md`. More templates: [`vibe-coding/workflow.md`](../../vibe-coding/workflow.md).

```text
Read `AGENTS.md` and `docs/phases/phase-5-payments-and-lab-tests/README.md` first.

Implement ONLY Payments & Lab Tests (Phase 5). Do not build anything from later phases.

Work in this order and stop after each step so I can test it:
1. Database: Prisma models + migration for this phase.
2. Backend: module, DTOs, service, controller, guards, unit tests, Swagger decorators.
3. Frontend: RTK Query endpoints, pages, components, forms, GSAP touches.
4. Tick the boxes in `docs/phases/phase-5-payments-and-lab-tests/checklist.md` for everything that works.

Pick the payment gateway with the team first (see docs/architecture/deployment.md). Build the mock provider before any real gateway.
```

---

← [Phase 4: Online Consultation, Diagnosis & E-Prescriptions](../phase-4-consultation-and-prescriptions/README.md) · [Phase 6: Integration, Testing & Deployment](../phase-6-integration-and-deployment/README.md) → · [Checklist](checklist.md)
