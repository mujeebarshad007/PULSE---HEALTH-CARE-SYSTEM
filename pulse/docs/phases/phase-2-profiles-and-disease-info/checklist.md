# Phase 2 Checklist: Profiles & Disease Information

**Status:** ⬜ Not started · **Owner(s):** _assign_ · **Started:** _date_ · **Finished:** _date_

Plan: [README.md](README.md) · Target window: 2026-11-14 → 2026-11-20

## Database

- [ ] Models: `PatientProfile`, `DoctorProfile` (specialization, qualifications, experienceYears, licenseNumber, clinic, city, area, address, consultationFee, offersOnline, bio, isVerified)
- [ ] Models: `Specialization`, `Disease` (slug, overview, causes, treatment, prevention, specializationId), `Symptom`, `DiseaseSymptom` (join)
- [ ] Indexes: doctor city / specialization, disease name / slug
- [ ] Seed: specializations, a starter set of symptoms and diseases

## Backend

- [ ] `patients` module: own profile read / update; admin read / update
- [ ] `doctors` module: own profile read / update; public profile read (verified doctors only); admin edit and verify
- [ ] `specializations` module: public list; admin create / update / delete
- [ ] `diseases` module: public list with search and filters, public detail by slug; admin CRUD
- [ ] `symptoms` module: public list; admin CRUD; disease-symptom links managed from the disease admin
- [ ] Profile rows are created lazily on first save (upsert); reading an empty profile returns defaults

## Frontend

- [ ] Patient profile page; doctor profile edit page; onboarding banner 'complete your profile'
- [ ] Public doctor profile page `/doctors/[doctorId]` (booking button is added in Phase 3)
- [ ] Public disease browse `/diseases` with search and symptom filter; disease detail page
- [ ] Admin: specializations, symptoms and diseases management pages; admin user detail page with doctor verification
- [ ] Reusable `SpecializationSelect` and `SymptomPicker` components
- [ ] GSAP: card grid stagger, detail-page reveal

## Endpoints working (tested in Swagger)

- [ ] `GET /patients/me` (Patient)
- [ ] `PUT /patients/me` (Patient)
- [ ] `GET /patients/:id` (Admin)
- [ ] `PUT /patients/:id` (Admin)
- [ ] `GET /doctors/me` (Doctor)
- [ ] `PUT /doctors/me` (Doctor)
- [ ] `GET /doctors/:id` (Public (verified doctors only))
- [ ] `PUT /doctors/:id` (Admin)
- [ ] `PATCH /doctors/:id/verification` (Admin)
- [ ] `GET /specializations` (Public)
- [ ] `POST /specializations` (Admin)
- [ ] `PATCH /specializations/:id` (Admin)
- [ ] `DELETE /specializations/:id` (Admin)
- [ ] `GET /diseases` (Public)
- [ ] `GET /diseases/:slug` (Public)
- [ ] `POST /diseases` (Admin)
- [ ] `PATCH /diseases/:id` (Admin)
- [ ] `DELETE /diseases/:id` (Admin)
- [ ] `GET /symptoms` (Public)
- [ ] `POST /symptoms` (Admin)
- [ ] `PATCH /symptoms/:id` (Admin)
- [ ] `DELETE /symptoms/:id` (Admin)

## Tests

- [ ] A patient can only read / update their own profile
- [ ] Unverified doctors do not appear on public pages or public endpoints
- [ ] Only Admin can verify a doctor or change disease data
- [ ] Disease search by name and by symptom returns the expected diseases
- [ ] Deleting a specialization that is in use is blocked with a clear error

## Definition of done

- [ ] Doctor completes a profile, Admin verifies it, and it becomes publicly visible
- [ ] Disease information is browsable without logging in
- [ ] UC-03, UC-04, UC-15 and doctor verification pass their acceptance check

## Viva / demo preparation

- [ ] Every member can explain: Why `DoctorProfile` is separate from `User` (one login table, role-specific data in profile tables)
- [ ] Every member can explain: Why diseases link to specializations (this is what makes symptom-based doctor search possible in Phase 3)
- [ ] Every member can explain: Why unverified doctors are hidden (patient safety)

## Sign-off

- [ ] Reviewed by another team member: ______
- [ ] `develop` merged into `main`
- [ ] Tagged `v0.2.0`
- [ ] `CHANGELOG.md` updated
