# Phase 3 Checklist: Doctor Search, Schedules & Appointment Booking

**Status:** ⬜ Not started · **Owner(s):** _assign_ · **Started:** _date_ · **Finished:** _date_

Plan: [README.md](README.md) · Target window: 2026-11-21 → 2026-11-27

## Database

- [ ] Enums: `AppointmentType` (PHYSICAL, ONLINE), `AppointmentStatus` (PENDING, CONFIRMED, REJECTED, CANCELLED, COMPLETED)
- [ ] Models: `DoctorAvailability` (dayOfWeek, startTime, endTime, slotMinutes), `TimeOff` (doctor, date range, reason), `Appointment` (patient, doctor, type, startsAt, endsAt, status, reason, meetingUrl, fee)
- [ ] Unique index on doctor + startsAt for active appointments; indexes on patient and doctor lookups

## Backend

- [ ] Doctor search on `GET /doctors`: text, specialization, symptom, city / area, consultation mode, fee range, sort, pagination
- [ ] `schedules` module: weekly availability windows, slot length, time-off dates
- [ ] Slot generator: available slots = weekly windows − time off − existing active appointments (slots are computed, not stored)
- [ ] `appointments` module: create, list (role-scoped), detail, confirm, reject, cancel, complete
- [ ] Appointment state machine kept in one file; invalid transitions are rejected
- [ ] Double-booking prevention enforced in the database (unique constraint) and inside a transaction

## Frontend

- [ ] `/doctors` search page: filters, results grid, empty state, pagination
- [ ] Doctor profile gets the slot picker and 'Book appointment' button
- [ ] Booking flow `/patient/book/[doctorId]`: mode → date → slot → reason / symptoms → confirm
- [ ] Patient appointments list and detail (cancel action)
- [ ] Doctor schedule editor (weekly windows, time off) and appointment inbox (confirm / reject / complete)
- [ ] GSAP: slot selection transitions, list stagger, status-change animation

## Endpoints working (tested in Swagger)

- [ ] `GET /doctors` (Public)
- [ ] `GET /doctors/:id/slots?date=` (Public)
- [ ] `GET /schedules/me` (Doctor)
- [ ] `PUT /schedules/me/availability` (Doctor)
- [ ] `POST /schedules/me/time-off` (Doctor)
- [ ] `DELETE /schedules/me/time-off/:id` (Doctor)
- [ ] `POST /appointments` (Patient)
- [ ] `GET /appointments` (Patient / Doctor / Admin (scoped))
- [ ] `GET /appointments/:id` (Participants, Admin (metadata))
- [ ] `PATCH /appointments/:id/confirm` (Doctor)
- [ ] `PATCH /appointments/:id/reject` (Doctor)
- [ ] `PATCH /appointments/:id/cancel` (Patient / Doctor)
- [ ] `PATCH /appointments/:id/complete` (Doctor)

## Tests

- [ ] Two patients booking the same slot at the same time: exactly one succeeds
- [ ] Slots inside time off, outside working hours, or in the past are never offered
- [ ] Symptom search returns doctors of the mapped specialization
- [ ] State machine: REJECTED / CANCELLED / COMPLETED cannot change again
- [ ] A patient cannot see or change another patient's appointment

## Definition of done

- [ ] End to end: search → open profile → pick slot → book → doctor confirms → patient sees CONFIRMED
- [ ] No double-booking possible, proven by a concurrency test
- [ ] UC-05, UC-06, UC-08 (doctor side) and UC-10 pass their acceptance check

## Viva / demo preparation

- [ ] Every member can explain: Why slots are computed instead of stored (no stale rows, easy schedule changes)
- [ ] Every member can explain: How double-booking is prevented (DB constraint + transaction, not just an `if` in code)
- [ ] Every member can explain: The appointment status flow and who is allowed to trigger each transition

## Sign-off

- [ ] Reviewed by another team member: ______
- [ ] `develop` merged into `main`
- [ ] Tagged `v0.3.0`
- [ ] `CHANGELOG.md` updated
