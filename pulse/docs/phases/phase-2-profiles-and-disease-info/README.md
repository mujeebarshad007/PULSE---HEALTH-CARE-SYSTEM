# Phase 2: Profiles & Disease Information

> **Increment:** Increment 2 (Sprint 2, part 1) · **Target window:** 2026-11-14 → 2026-11-20 · **Status:** ⬜ Not started

> This document is a plan. It contains **no application code**.

## Goal

Patients and doctors can manage their profiles, admins can verify doctors and curate specializations, diseases and symptoms, and anyone can browse disease information and public doctor profiles.

## Use cases covered

- UC-03
- UC-04
- UC-15
- UC-14 (doctor verification)

**Roles involved:** Patient, Doctor, Admin, Guest (read-only public pages)

## Decisions made for this phase

- Symptom search works as symptom → diseases → specializations → doctors (defined here, used in Phase 3).

Change these here first if the team disagrees, then update the checklist.

## Scope

### Database

- Models: `PatientProfile`, `DoctorProfile` (specialization, qualifications, experienceYears, licenseNumber, clinic, city, area, address, consultationFee, offersOnline, bio, isVerified)
- Models: `Specialization`, `Disease` (slug, overview, causes, treatment, prevention, specializationId), `Symptom`, `DiseaseSymptom` (join)
- Indexes: doctor city / specialization, disease name / slug
- Seed: specializations, a starter set of symptoms and diseases

### Backend

- `patients` module: own profile read / update; admin read / update
- `doctors` module: own profile read / update; public profile read (verified doctors only); admin edit and verify
- `specializations` module: public list; admin create / update / delete
- `diseases` module: public list with search and filters, public detail by slug; admin CRUD
- `symptoms` module: public list; admin CRUD; disease-symptom links managed from the disease admin
- Profile rows are created lazily on first save (upsert); reading an empty profile returns defaults

### Frontend

- Patient profile page; doctor profile edit page; onboarding banner 'complete your profile'
- Public doctor profile page `/doctors/[doctorId]` (booking button is added in Phase 3)
- Public disease browse `/diseases` with search and symptom filter; disease detail page
- Admin: specializations, symptoms and diseases management pages; admin user detail page with doctor verification
- Reusable `SpecializationSelect` and `SymptomPicker` components
- GSAP: card grid stagger, detail-page reveal

## API endpoints

Base path `/api/v1`. Conventions: [`api-design.md`](../../architecture/api-design.md). Permissions: [`roles-and-permissions.md`](../../project/roles-and-permissions.md).

| Method | Path | Access | Use case |
| --- | --- | --- | --- |
| `GET` | `/patients/me` | Patient | UC-04 |
| `PUT` | `/patients/me` | Patient | UC-04 |
| `GET` | `/patients/:id` | Admin | UC-04 |
| `PUT` | `/patients/:id` | Admin | UC-04 |
| `GET` | `/doctors/me` | Doctor | UC-03 |
| `PUT` | `/doctors/me` | Doctor | UC-03 |
| `GET` | `/doctors/:id` | Public (verified doctors only) | UC-03 |
| `PUT` | `/doctors/:id` | Admin | UC-03 |
| `PATCH` | `/doctors/:id/verification` | Admin | UC-14 |
| `GET` | `/specializations` | Public | UC-03 |
| `POST` | `/specializations` | Admin | UC-15 |
| `PATCH` | `/specializations/:id` | Admin | UC-15 |
| `DELETE` | `/specializations/:id` | Admin | UC-15 |
| `GET` | `/diseases` | Public | UC-15 |
| `GET` | `/diseases/:slug` | Public | UC-15 |
| `POST` | `/diseases` | Admin | UC-15 |
| `PATCH` | `/diseases/:id` | Admin | UC-15 |
| `DELETE` | `/diseases/:id` | Admin | UC-15 |
| `GET` | `/symptoms` | Public | UC-15 |
| `POST` | `/symptoms` | Admin | UC-15 |
| `PATCH` | `/symptoms/:id` | Admin | UC-15 |
| `DELETE` | `/symptoms/:id` | Admin | UC-15 |

## Files in this phase

Every file below already exists in the repo as a placeholder tagged with this phase. Fill them in; do not move them.

<details>
<summary><b>Backend</b> (31 files)</summary>

| File | Purpose |
| --- | --- |
| `backend/src/modules/patients/patients.module.ts` | Patient profile - module wiring |
| `backend/src/modules/patients/patients.controller.ts` | Patient profile - HTTP routes, Swagger docs, role decorators |
| `backend/src/modules/patients/patients.service.ts` | Patient profile - business logic and database access |
| `backend/src/modules/patients/patients.service.spec.ts` | Patient profile - unit tests for the service |
| `backend/src/modules/patients/dto/update-patient-profile.dto.ts` | Patient profile fields (dob, gender, blood group, address, emergency contact) |
| `backend/src/modules/doctors/doctors.module.ts` | Doctor profile and search - module wiring |
| `backend/src/modules/doctors/doctors.controller.ts` | Doctor profile and search - HTTP routes, Swagger docs, role decorators |
| `backend/src/modules/doctors/doctors.service.ts` | Doctor profile and search - business logic and database access |
| `backend/src/modules/doctors/doctors.service.spec.ts` | Doctor profile and search - unit tests for the service |
| `backend/src/modules/doctors/dto/update-doctor-profile.dto.ts` | Doctor profile fields (specialization, qualifications, experience, clinic, fee, contact) |
| `backend/src/modules/doctors/dto/verify-doctor.dto.ts` | Admin verification decision |
| `backend/src/modules/specializations/specializations.module.ts` | Specializations - module wiring |
| `backend/src/modules/specializations/specializations.controller.ts` | Specializations - HTTP routes, Swagger docs, role decorators |
| `backend/src/modules/specializations/specializations.service.ts` | Specializations - business logic and database access |
| `backend/src/modules/specializations/specializations.service.spec.ts` | Specializations - unit tests for the service |
| `backend/src/modules/specializations/dto/create-specialization.dto.ts` | Create payload |
| `backend/src/modules/specializations/dto/update-specialization.dto.ts` | Update payload |
| `backend/src/modules/diseases/diseases.module.ts` | Disease information - module wiring |
| `backend/src/modules/diseases/diseases.controller.ts` | Disease information - HTTP routes, Swagger docs, role decorators |
| `backend/src/modules/diseases/diseases.service.ts` | Disease information - business logic and database access |
| `backend/src/modules/diseases/diseases.service.spec.ts` | Disease information - unit tests for the service |
| `backend/src/modules/diseases/dto/create-disease.dto.ts` | Disease fields + symptom ids + specialization |
| `backend/src/modules/diseases/dto/update-disease.dto.ts` | Partial update |
| `backend/src/modules/diseases/dto/search-diseases-query.dto.ts` | Search by text, symptom, specialization |
| `backend/src/modules/symptoms/symptoms.module.ts` | Symptoms - module wiring |
| `backend/src/modules/symptoms/symptoms.controller.ts` | Symptoms - HTTP routes, Swagger docs, role decorators |
| `backend/src/modules/symptoms/symptoms.service.ts` | Symptoms - business logic and database access |
| `backend/src/modules/symptoms/symptoms.service.spec.ts` | Symptoms - unit tests for the service |
| `backend/src/modules/symptoms/dto/create-symptom.dto.ts` | Create payload |
| `backend/src/modules/symptoms/dto/update-symptom.dto.ts` | Update payload |
| `backend/test/profiles-and-diseases.e2e-spec.ts` | E2E: profiles, doctor verification, disease CRUD and search |

</details>

<details>
<summary><b>Frontend</b> (34 files)</summary>

| File | Purpose |
| --- | --- |
| `frontend/src/app/(public)/doctors/[doctorId]/page.tsx` | Public doctor profile (slot picker added in Phase 3) |
| `frontend/src/app/(public)/diseases/page.tsx` | Browse and search diseases |
| `frontend/src/app/(public)/diseases/[slug]/page.tsx` | Disease detail |
| `frontend/src/app/(dashboard)/patient/profile/page.tsx` | Edit patient profile |
| `frontend/src/app/(dashboard)/doctor/profile/page.tsx` | Edit doctor profile |
| `frontend/src/app/(dashboard)/admin/users/[userId]/page.tsx` | User detail with doctor verification |
| `frontend/src/app/(dashboard)/admin/specializations/page.tsx` | Manage specializations |
| `frontend/src/app/(dashboard)/admin/symptoms/page.tsx` | Manage symptoms |
| `frontend/src/app/(dashboard)/admin/diseases/page.tsx` | Manage diseases |
| `frontend/src/app/(dashboard)/admin/diseases/[diseaseId]/page.tsx` | Edit disease and linked symptoms |
| `frontend/src/features/patients/components/patient-profile-form.tsx` | Patient profile form |
| `frontend/src/features/patients/api/patients.api.ts` | RTK Query endpoints for patients |
| `frontend/src/features/patients/types.ts` | TypeScript types for patients (mirror the API DTOs) |
| `frontend/src/features/patients/schemas.ts` | Zod schema for patient profile |
| `frontend/src/features/doctors/components/doctor-profile-form.tsx` | Doctor profile form |
| `frontend/src/features/doctors/components/doctor-profile-view.tsx` | Public doctor profile view |
| `frontend/src/features/doctors/components/doctor-card.tsx` | Doctor summary card |
| `frontend/src/features/doctors/components/doctor-verification-panel.tsx` | Admin verification controls |
| `frontend/src/features/doctors/api/doctors.api.ts` | RTK Query endpoints for doctors |
| `frontend/src/features/doctors/types.ts` | TypeScript types for doctors (mirror the API DTOs) |
| `frontend/src/features/doctors/schemas.ts` | Zod schema for doctor profile |
| `frontend/src/features/specializations/components/specialization-select.tsx` | Specialization dropdown |
| `frontend/src/features/specializations/components/specialization-form.tsx` | Admin create / edit form |
| `frontend/src/features/specializations/api/specializations.api.ts` | RTK Query endpoints for specializations |
| `frontend/src/features/specializations/types.ts` | TypeScript types for specializations (mirror the API DTOs) |
| `frontend/src/features/diseases/components/disease-card.tsx` | Disease card |
| `frontend/src/features/diseases/components/disease-detail.tsx` | Disease detail view |
| `frontend/src/features/diseases/components/disease-search.tsx` | Search bar + symptom filter |
| `frontend/src/features/diseases/components/disease-form.tsx` | Admin disease form |
| `frontend/src/features/diseases/components/symptom-picker.tsx` | Multi-select for symptoms |
| `frontend/src/features/diseases/api/diseases.api.ts` | RTK Query endpoints for diseases |
| `frontend/src/features/diseases/api/symptoms.api.ts` | RTK Query endpoints for diseases |
| `frontend/src/features/diseases/types.ts` | TypeScript types for diseases (mirror the API DTOs) |
| `frontend/src/features/diseases/schemas.ts` | Zod schemas for disease / symptom forms |

</details>

## Test scenarios

- A patient can only read / update their own profile
- Unverified doctors do not appear on public pages or public endpoints
- Only Admin can verify a doctor or change disease data
- Disease search by name and by symptom returns the expected diseases
- Deleting a specialization that is in use is blocked with a clear error

## Definition of done

- [ ] Doctor completes a profile, Admin verifies it, and it becomes publicly visible
- [ ] Disease information is browsable without logging in
- [ ] UC-03, UC-04, UC-15 and doctor verification pass their acceptance check

## Be ready to explain (viva / demo)

- Why `DoctorProfile` is separate from `User` (one login table, role-specific data in profile tables)
- Why diseases link to specializations (this is what makes symptom-based doctor search possible in Phase 3)
- Why unverified doctors are hidden (patient safety)

## Stretch goals (not part of the MVP)

- Doctor profile photo upload
- Doctor qualification documents for verification

## Vibe-coding prompt

Paste into your AI assistant after loading `AGENTS.md`. More templates: [`vibe-coding/workflow.md`](../../vibe-coding/workflow.md).

```text
Read `AGENTS.md` and `docs/phases/phase-2-profiles-and-disease-info/README.md` first.

Implement ONLY Profiles & Disease Information (Phase 2). Do not build anything from later phases.

Work in this order and stop after each step so I can test it:
1. Database: Prisma models + migration for this phase.
2. Backend: module, DTOs, service, controller, guards, unit tests, Swagger decorators.
3. Frontend: RTK Query endpoints, pages, components, forms, GSAP touches.
4. Tick the boxes in `docs/phases/phase-2-profiles-and-disease-info/checklist.md` for everything that works.

Public endpoints must never return unverified doctors.
```

---

← [Phase 1: Authentication & User Accounts](../phase-1-authentication/README.md) · [Phase 3: Doctor Search, Schedules & Appointment Booking](../phase-3-search-and-booking/README.md) → · [Checklist](checklist.md)
