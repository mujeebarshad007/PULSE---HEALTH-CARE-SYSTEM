# Phase 4: Online Consultation, Diagnosis & E-Prescriptions

> **Increment:** Increment 4 (Sprint 3 part 2) · **Target window:** 2026-11-28 → 2026-12-02 · **Status:** ⬜ Not started

> This document is a plan. It contains **no application code**.

## Goal

Doctors record a diagnosis for a confirmed appointment (including online consultations) and issue electronic prescriptions; patients see them in their account; doctors can review a patient's appointment history.

## Use cases covered

- UC-09
- UC-16

**Roles involved:** Doctor (verified), Patient

## Decisions made for this phase

- Online consultation MVP = doctor adds an external meeting link; in-app chat / video is a stretch goal.
- Online appointments must be PAID (Phase 5) before the meeting link is shown to the patient.

Change these here first if the team disagrees, then update the checklist.

## Scope

### Database

- Models: `Consultation` (appointment, diagnosis, notes, meetingUrl), `Prescription` (appointment, doctor, patient, issuedAt, notes), `PrescriptionItem` (medicine, dosage, frequency, duration, instructions)
- One consultation and one prescription per appointment (unique constraints)

### Backend

- `consultations` module: doctor saves diagnosis notes and, for ONLINE appointments, a meeting link; participants can read
- `prescriptions` module: verified doctor issues a prescription (medicine, dosage, frequency, duration, instructions) for their own appointment
- Patient endpoints: list and read own prescriptions; doctor endpoints: list issued prescriptions
- Doctor patient-history endpoint: appointment history for a patient the doctor has a relationship with
- Relationship rule helper: doctor has at least one CONFIRMED or COMPLETED appointment with that patient

### Frontend

- Doctor appointment detail becomes a consultation panel: diagnosis form + prescription builder
- Patient appointment detail shows diagnosis, meeting link (online) and prescription
- Patient prescriptions list and detail with a print-friendly view
- Doctor patient-history page
- GSAP: panel transitions, prescription item add / remove animation

## API endpoints

Base path `/api/v1`. Conventions: [`api-design.md`](../../architecture/api-design.md). Permissions: [`roles-and-permissions.md`](../../project/roles-and-permissions.md).

| Method | Path | Access | Use case |
| --- | --- | --- | --- |
| `GET` | `/appointments/:id/consultation` | Participants | UC-09 |
| `PUT` | `/appointments/:id/consultation` | Doctor (own appointment) | UC-09 |
| `POST` | `/appointments/:id/prescription` | Verified Doctor (own appointment) | UC-16 |
| `GET` | `/prescriptions` | Patient (own) / Doctor (issued) | UC-16 |
| `GET` | `/prescriptions/:id` | Patient (own) / issuing Doctor | UC-16 |
| `GET` | `/doctors/me/patients/:patientId/history` | Doctor (with relationship) | UC-09 |

## Files in this phase

Every file below already exists in the repo as a placeholder tagged with this phase. Fill them in; do not move them.

<details>
<summary><b>Backend</b> (13 files)</summary>

| File | Purpose |
| --- | --- |
| `backend/src/modules/doctors/doctor-patient-relationship.service.ts` | Rule: doctor may see a patient's history only with a CONFIRMED / COMPLETED appointment |
| `backend/src/modules/consultations/consultations.module.ts` | Consultation and diagnosis - module wiring |
| `backend/src/modules/consultations/consultations.controller.ts` | Consultation and diagnosis - HTTP routes, Swagger docs, role decorators |
| `backend/src/modules/consultations/consultations.service.ts` | Consultation and diagnosis - business logic and database access |
| `backend/src/modules/consultations/consultations.service.spec.ts` | Consultation and diagnosis - unit tests for the service |
| `backend/src/modules/consultations/dto/save-consultation.dto.ts` | Diagnosis, notes, meeting link |
| `backend/src/modules/prescriptions/prescriptions.module.ts` | Electronic prescriptions - module wiring |
| `backend/src/modules/prescriptions/prescriptions.controller.ts` | Electronic prescriptions - HTTP routes, Swagger docs, role decorators |
| `backend/src/modules/prescriptions/prescriptions.service.ts` | Electronic prescriptions - business logic and database access |
| `backend/src/modules/prescriptions/prescriptions.service.spec.ts` | Electronic prescriptions - unit tests for the service |
| `backend/src/modules/prescriptions/dto/create-prescription.dto.ts` | Prescription with items |
| `backend/src/modules/prescriptions/dto/prescription-item.dto.ts` | One medicine line |
| `backend/test/consultation-and-prescriptions.e2e-spec.ts` | E2E: diagnosis, prescriptions, privacy rules |

</details>

<details>
<summary><b>Frontend</b> (17 files)</summary>

| File | Purpose |
| --- | --- |
| `frontend/src/app/(dashboard)/patient/prescriptions/page.tsx` | My prescriptions |
| `frontend/src/app/(dashboard)/patient/prescriptions/[prescriptionId]/page.tsx` | Prescription detail, print-friendly |
| `frontend/src/app/(dashboard)/doctor/patients/[patientId]/page.tsx` | Patient history (lab results section arrives in Phase 5) |
| `frontend/src/app/(dashboard)/doctor/prescriptions/page.tsx` | Prescriptions I issued |
| `frontend/src/features/consultations/components/consultation-panel.tsx` | Doctor panel: diagnosis + prescription |
| `frontend/src/features/consultations/components/diagnosis-form.tsx` | Diagnosis form |
| `frontend/src/features/consultations/components/meeting-link-card.tsx` | Show / set meeting link (hidden until paid) |
| `frontend/src/features/consultations/components/patient-history-view.tsx` | Doctor view of a patient's history |
| `frontend/src/features/consultations/api/consultations.api.ts` | RTK Query endpoints for consultations |
| `frontend/src/features/consultations/types.ts` | TypeScript types for consultations (mirror the API DTOs) |
| `frontend/src/features/consultations/schemas.ts` | Zod schema for diagnosis |
| `frontend/src/features/prescriptions/components/prescription-builder.tsx` | Add / remove medicine lines |
| `frontend/src/features/prescriptions/components/prescription-view.tsx` | Read-only, print-friendly prescription |
| `frontend/src/features/prescriptions/components/prescription-list.tsx` | List |
| `frontend/src/features/prescriptions/api/prescriptions.api.ts` | RTK Query endpoints for prescriptions |
| `frontend/src/features/prescriptions/types.ts` | TypeScript types for prescriptions (mirror the API DTOs) |
| `frontend/src/features/prescriptions/schemas.ts` | Zod schema for prescription |

</details>

## Test scenarios

- Only the appointment's doctor can write a diagnosis or prescription
- Unverified or suspended doctors cannot issue prescriptions
- A doctor cannot open the history of a patient they have never treated
- Diagnosis and prescription are invisible to other patients and to Admin
- A prescription cannot be created twice for the same appointment

## Definition of done

- [ ] Confirmed appointment → diagnosis saved → prescription issued → patient can read both
- [ ] Medical content is visible only to the involved patient and doctor
- [ ] UC-09 and UC-16 pass their acceptance check

## Be ready to explain (viva / demo)

- Why online consultation uses a meeting link in the MVP (simple, reliable, no video infrastructure to maintain)
- Why Admin cannot read medical content (least privilege)
- How the doctor-patient relationship rule protects patient privacy

## Stretch goals (not part of the MVP)

- In-app real-time chat for online consultations
- Prescription PDF export
- Follow-up appointment suggestion

## Vibe-coding prompt

Paste into your AI assistant after loading `AGENTS.md`. More templates: [`vibe-coding/workflow.md`](../../vibe-coding/workflow.md).

```text
Read `AGENTS.md` and `docs/phases/phase-4-consultation-and-prescriptions/README.md` first.

Implement ONLY Online Consultation, Diagnosis & E-Prescriptions (Phase 4). Do not build anything from later phases.

Work in this order and stop after each step so I can test it:
1. Database: Prisma models + migration for this phase.
2. Backend: module, DTOs, service, controller, guards, unit tests, Swagger decorators.
3. Frontend: RTK Query endpoints, pages, components, forms, GSAP touches.
4. Tick the boxes in `docs/phases/phase-4-consultation-and-prescriptions/checklist.md` for everything that works.

Admin must not be able to read diagnosis or prescription content.
```

---

← [Phase 3: Doctor Search, Schedules & Appointment Booking](../phase-3-search-and-booking/README.md) · [Phase 5: Payments & Lab Tests](../phase-5-payments-and-lab-tests/README.md) → · [Checklist](checklist.md)
