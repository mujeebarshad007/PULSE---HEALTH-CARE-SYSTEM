# Phase 4 Checklist: Online Consultation, Diagnosis & E-Prescriptions

**Status:** ⬜ Not started · **Owner(s):** _assign_ · **Started:** _date_ · **Finished:** _date_

Plan: [README.md](README.md) · Target window: 2026-11-28 → 2026-12-02

## Database

- [ ] Models: `Consultation` (appointment, diagnosis, notes, meetingUrl), `Prescription` (appointment, doctor, patient, issuedAt, notes), `PrescriptionItem` (medicine, dosage, frequency, duration, instructions)
- [ ] One consultation and one prescription per appointment (unique constraints)

## Backend

- [ ] `consultations` module: doctor saves diagnosis notes and, for ONLINE appointments, a meeting link; participants can read
- [ ] `prescriptions` module: verified doctor issues a prescription (medicine, dosage, frequency, duration, instructions) for their own appointment
- [ ] Patient endpoints: list and read own prescriptions; doctor endpoints: list issued prescriptions
- [ ] Doctor patient-history endpoint: appointment history for a patient the doctor has a relationship with
- [ ] Relationship rule helper: doctor has at least one CONFIRMED or COMPLETED appointment with that patient

## Frontend

- [ ] Doctor appointment detail becomes a consultation panel: diagnosis form + prescription builder
- [ ] Patient appointment detail shows diagnosis, meeting link (online) and prescription
- [ ] Patient prescriptions list and detail with a print-friendly view
- [ ] Doctor patient-history page
- [ ] GSAP: panel transitions, prescription item add / remove animation

## Endpoints working (tested in Swagger)

- [ ] `GET /appointments/:id/consultation` (Participants)
- [ ] `PUT /appointments/:id/consultation` (Doctor (own appointment))
- [ ] `POST /appointments/:id/prescription` (Verified Doctor (own appointment))
- [ ] `GET /prescriptions` (Patient (own) / Doctor (issued))
- [ ] `GET /prescriptions/:id` (Patient (own) / issuing Doctor)
- [ ] `GET /doctors/me/patients/:patientId/history` (Doctor (with relationship))

## Tests

- [ ] Only the appointment's doctor can write a diagnosis or prescription
- [ ] Unverified or suspended doctors cannot issue prescriptions
- [ ] A doctor cannot open the history of a patient they have never treated
- [ ] Diagnosis and prescription are invisible to other patients and to Admin
- [ ] A prescription cannot be created twice for the same appointment

## Definition of done

- [ ] Confirmed appointment → diagnosis saved → prescription issued → patient can read both
- [ ] Medical content is visible only to the involved patient and doctor
- [ ] UC-09 and UC-16 pass their acceptance check

## Viva / demo preparation

- [ ] Every member can explain: Why online consultation uses a meeting link in the MVP (simple, reliable, no video infrastructure to maintain)
- [ ] Every member can explain: Why Admin cannot read medical content (least privilege)
- [ ] Every member can explain: How the doctor-patient relationship rule protects patient privacy

## Sign-off

- [ ] Reviewed by another team member: ______
- [ ] `develop` merged into `main`
- [ ] Tagged `v0.4.0`
- [ ] `CHANGELOG.md` updated
