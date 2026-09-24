# Phase 3: Doctor Search, Schedules & Appointment Booking

> **Increment:** Increment 3 (Sprint 2 part 2 + Sprint 3) · **Target window:** 2026-11-21 → 2026-11-27 · **Status:** ⬜ Not started

> This document is a plan. It contains **no application code**.

## Goal

Patients find a doctor by specialization, symptom or location and book a physical or online appointment in an available slot; doctors define their schedule and confirm or reject requests.

## Use cases covered

- UC-05
- UC-06
- UC-08 (doctor side)
- UC-10

**Roles involved:** Guest (search), Patient, Doctor, Admin (read)

## Decisions made for this phase

- Appointments start as PENDING; the doctor confirms or rejects (matches the 'Confirm Appointment' use case).
- Physical and online appointments share the same booking flow; the type changes what happens later.
- The double-booking rule needs a partial unique index (only PENDING / CONFIRMED rows count). If the Prisma schema cannot express it in your version, create the migration with `--create-only`, add the index as raw SQL in the migration file, then apply it.

Change these here first if the team disagrees, then update the checklist.

## Scope

### Database

- Enums: `AppointmentType` (PHYSICAL, ONLINE), `AppointmentStatus` (PENDING, CONFIRMED, REJECTED, CANCELLED, COMPLETED)
- Models: `DoctorAvailability` (dayOfWeek, startTime, endTime, slotMinutes), `TimeOff` (doctor, date range, reason), `Appointment` (patient, doctor, type, startsAt, endsAt, status, reason, meetingUrl, fee)
- Unique index on doctor + startsAt for active appointments; indexes on patient and doctor lookups

### Backend

- Doctor search on `GET /doctors`: text, specialization, symptom, city / area, consultation mode, fee range, sort, pagination
- `schedules` module: weekly availability windows, slot length, time-off dates
- Slot generator: available slots = weekly windows − time off − existing active appointments (slots are computed, not stored)
- `appointments` module: create, list (role-scoped), detail, confirm, reject, cancel, complete
- Appointment state machine kept in one file; invalid transitions are rejected
- Double-booking prevention enforced in the database (unique constraint) and inside a transaction

### Frontend

- `/doctors` search page: filters, results grid, empty state, pagination
- Doctor profile gets the slot picker and 'Book appointment' button
- Booking flow `/patient/book/[doctorId]`: mode → date → slot → reason / symptoms → confirm
- Patient appointments list and detail (cancel action)
- Doctor schedule editor (weekly windows, time off) and appointment inbox (confirm / reject / complete)
- GSAP: slot selection transitions, list stagger, status-change animation

## API endpoints

Base path `/api/v1`. Conventions: [`api-design.md`](../../architecture/api-design.md). Permissions: [`roles-and-permissions.md`](../../project/roles-and-permissions.md).

| Method | Path | Access | Use case |
| --- | --- | --- | --- |
| `GET` | `/doctors` | Public | UC-05 |
| `GET` | `/doctors/:id/slots?date=` | Public | UC-06 |
| `GET` | `/schedules/me` | Doctor | UC-10 |
| `PUT` | `/schedules/me/availability` | Doctor | UC-10 |
| `POST` | `/schedules/me/time-off` | Doctor | UC-10 |
| `DELETE` | `/schedules/me/time-off/:id` | Doctor | UC-10 |
| `POST` | `/appointments` | Patient | UC-06 |
| `GET` | `/appointments` | Patient / Doctor / Admin (scoped) | UC-06 |
| `GET` | `/appointments/:id` | Participants, Admin (metadata) | UC-06 |
| `PATCH` | `/appointments/:id/confirm` | Doctor | UC-08 |
| `PATCH` | `/appointments/:id/reject` | Doctor | UC-08 |
| `PATCH` | `/appointments/:id/cancel` | Patient / Doctor | UC-06 |
| `PATCH` | `/appointments/:id/complete` | Doctor | UC-08 |

## Files in this phase

Every file below already exists in the repo as a placeholder tagged with this phase. Fill them in; do not move them.

<details>
<summary><b>Backend</b> (20 files)</summary>

| File | Purpose |
| --- | --- |
| `backend/src/common/utils/date-time.util.ts` | Date / time helpers used by slot generation and validation |
| `backend/src/modules/doctors/dto/search-doctors-query.dto.ts` | Search filters: q, specialization, symptom, city, area, mode, fee range, sort |
| `backend/src/modules/schedules/schedules.module.ts` | Doctor schedules and slots - module wiring |
| `backend/src/modules/schedules/schedules.controller.ts` | Doctor schedules and slots - HTTP routes, Swagger docs, role decorators |
| `backend/src/modules/schedules/schedules.service.ts` | Doctor schedules and slots - business logic and database access |
| `backend/src/modules/schedules/schedules.service.spec.ts` | Doctor schedules and slots - unit tests for the service |
| `backend/src/modules/schedules/dto/set-availability.dto.ts` | Weekly windows and slot length |
| `backend/src/modules/schedules/dto/create-time-off.dto.ts` | Days off |
| `backend/src/modules/schedules/dto/get-slots-query.dto.ts` | Date query for available slots |
| `backend/src/modules/schedules/slot-generator.service.ts` | Compute free slots = availability - time off - active appointments |
| `backend/src/modules/schedules/slot-generator.service.spec.ts` | Unit tests for slot generation edge cases |
| `backend/src/modules/appointments/appointments.module.ts` | Appointments - module wiring |
| `backend/src/modules/appointments/appointments.controller.ts` | Appointments - HTTP routes, Swagger docs, role decorators |
| `backend/src/modules/appointments/appointments.service.ts` | Appointments - business logic and database access |
| `backend/src/modules/appointments/appointments.service.spec.ts` | Appointments - unit tests for the service |
| `backend/src/modules/appointments/dto/create-appointment.dto.ts` | Doctor, slot start, type, reason / symptoms |
| `backend/src/modules/appointments/dto/update-appointment-status.dto.ts` | Reject / cancel reason |
| `backend/src/modules/appointments/dto/list-appointments-query.dto.ts` | Filters: status, date range, type |
| `backend/src/modules/appointments/appointment-state-machine.ts` | Single place that defines allowed status transitions and who may trigger them |
| `backend/test/booking.e2e-spec.ts` | E2E: search, slots, booking, double-booking, state machine |

</details>

<details>
<summary><b>Frontend</b> (26 files)</summary>

| File | Purpose |
| --- | --- |
| `frontend/src/app/(public)/doctors/page.tsx` | Doctor search page with filters |
| `frontend/src/app/(dashboard)/patient/book/[doctorId]/page.tsx` | Booking flow: mode, date, slot, reason |
| `frontend/src/app/(dashboard)/patient/appointments/page.tsx` | My appointments |
| `frontend/src/app/(dashboard)/patient/appointments/[appointmentId]/page.tsx` | Appointment detail (diagnosis, meeting link, prescription arrive in Phase 4) |
| `frontend/src/app/(dashboard)/doctor/schedule/page.tsx` | Weekly availability and time off |
| `frontend/src/app/(dashboard)/doctor/appointments/page.tsx` | Appointment inbox: confirm / reject / complete |
| `frontend/src/app/(dashboard)/doctor/appointments/[appointmentId]/page.tsx` | Appointment detail (consultation panel arrives in Phase 4) |
| `frontend/src/components/common/status-badge.tsx` | Colour-coded status pill (appointment, lab, payment, user) |
| `frontend/src/features/doctors/components/doctor-search-filters.tsx` | Search filters |
| `frontend/src/features/doctors/components/doctor-search-results.tsx` | Results grid with pagination |
| `frontend/src/features/schedules/components/weekly-availability-editor.tsx` | Edit weekly windows |
| `frontend/src/features/schedules/components/time-off-manager.tsx` | Add / remove days off |
| `frontend/src/features/schedules/components/slot-picker.tsx` | Pick a slot on a date |
| `frontend/src/features/schedules/api/schedules.api.ts` | RTK Query endpoints for schedules |
| `frontend/src/features/schedules/types.ts` | TypeScript types for schedules (mirror the API DTOs) |
| `frontend/src/features/schedules/schemas.ts` | Zod schemas for availability / time off |
| `frontend/src/features/appointments/components/booking-form.tsx` | Booking form |
| `frontend/src/features/appointments/components/appointment-card.tsx` | Appointment summary card |
| `frontend/src/features/appointments/components/appointment-list.tsx` | List with status filter |
| `frontend/src/features/appointments/components/appointment-status-actions.tsx` | Confirm / reject / cancel / complete buttons by role and status |
| `frontend/src/features/appointments/components/appointment-detail.tsx` | Detail view |
| `frontend/src/features/appointments/api/appointments.api.ts` | RTK Query endpoints for appointments |
| `frontend/src/features/appointments/types.ts` | TypeScript types for appointments (mirror the API DTOs) |
| `frontend/src/features/appointments/schemas.ts` | Zod schema for booking |
| `frontend/src/lib/format.ts` | Format dates, times, currency |
| `frontend/src/hooks/use-debounce.ts` | Debounce search inputs |

</details>

## Test scenarios

- Two patients booking the same slot at the same time: exactly one succeeds
- Slots inside time off, outside working hours, or in the past are never offered
- Symptom search returns doctors of the mapped specialization
- State machine: REJECTED / CANCELLED / COMPLETED cannot change again
- A patient cannot see or change another patient's appointment

## Definition of done

- [ ] End to end: search → open profile → pick slot → book → doctor confirms → patient sees CONFIRMED
- [ ] No double-booking possible, proven by a concurrency test
- [ ] UC-05, UC-06, UC-08 (doctor side) and UC-10 pass their acceptance check

## Be ready to explain (viva / demo)

- Why slots are computed instead of stored (no stale rows, easy schedule changes)
- How double-booking is prevented (DB constraint + transaction, not just an `if` in code)
- The appointment status flow and who is allowed to trigger each transition

## Stretch goals (not part of the MVP)

- Reschedule flow
- Email / in-app notifications for status changes
- Doctor reviews and ratings

## Vibe-coding prompt

Paste into your AI assistant after loading `AGENTS.md`. More templates: [`vibe-coding/workflow.md`](../../vibe-coding/workflow.md).

```text
Read `AGENTS.md` and `docs/phases/phase-3-search-and-booking/README.md` first.

Implement ONLY Doctor Search, Schedules & Appointment Booking (Phase 3). Do not build anything from later phases.

Work in this order and stop after each step so I can test it:
1. Database: Prisma models + migration for this phase.
2. Backend: module, DTOs, service, controller, guards, unit tests, Swagger decorators.
3. Frontend: RTK Query endpoints, pages, components, forms, GSAP touches.
4. Tick the boxes in `docs/phases/phase-3-search-and-booking/checklist.md` for everything that works.

Compute slots on the fly. Enforce double-booking prevention at the database level.
```

---

← [Phase 2: Profiles & Disease Information](../phase-2-profiles-and-disease-info/README.md) · [Phase 4: Online Consultation, Diagnosis & E-Prescriptions](../phase-4-consultation-and-prescriptions/README.md) → · [Checklist](checklist.md)
