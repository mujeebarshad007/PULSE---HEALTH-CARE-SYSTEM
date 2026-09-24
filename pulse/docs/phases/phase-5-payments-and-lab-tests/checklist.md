# Phase 5 Checklist: Payments & Lab Tests

**Status:** ⬜ Not started · **Owner(s):** _assign_ · **Started:** _date_ · **Finished:** _date_

Plan: [README.md](README.md) · Target window: 2026-12-03 → 2026-12-07

## Database

- [ ] Enums: `LabBookingStatus` (PENDING, CONFIRMED, COMPLETED, CANCELLED), `PaymentStatus` (PENDING, PAID, FAILED, REFUNDED), `PaymentPurpose` (APPOINTMENT, LAB_BOOKING)
- [ ] Models: `Lab`, `LabTest` (price, description, preparation notes), `LabTechnicianProfile` (user → lab), `LabBooking`, `LabResult` (file key, mime type, size, summary, uploadedBy), `Payment` (purpose, amount, currency, provider, providerRef, status, paidAt)

## Backend

- [ ] `labs` module: labs and test catalogue; public search; admin manages labs, tests and technician assignment
- [ ] `lab-bookings` module: patient books a test; lab technician confirms, completes or cancels
- [ ] `lab-results` module: technician uploads a report file; patient and related doctor read it
- [ ] `storage` module: provider interface with a local-disk driver (dev) and a cloud-storage driver (production); files are never public
- [ ] `payments` module: create payment for an appointment or a lab booking, gateway callback, status, history
- [ ] Payment provider adapter: mock provider for development and demos, real gateway plugged in behind the same interface
- [ ] Doctor endpoint for a patient's lab results (needs the Phase 4 relationship rule)

## Frontend

- [ ] Lab and lab-test search pages, lab detail with its tests
- [ ] Lab booking flow for patients; patient lab bookings and lab results pages with a result viewer
- [ ] Lab technician dashboard: booking queue, confirm, upload result
- [ ] Admin: labs and lab-test catalogue management, technician assignment
- [ ] Checkout panel, payment status page, payment history
- [ ] Doctor patient-history page gains a lab results section
- [ ] GSAP: checkout progress, upload progress, result-ready reveal

## DevOps

- [ ] Configure cloud file storage and payment sandbox credentials in staging

## Endpoints working (tested in Swagger)

- [ ] `GET /labs` (Public)
- [ ] `GET /labs/:id` (Public)
- [ ] `POST /labs` (Admin)
- [ ] `PATCH /labs/:id` (Admin)
- [ ] `PATCH /labs/:id/technicians/:userId` (Admin)
- [ ] `GET /lab-tests` (Public)
- [ ] `POST /labs/:id/tests` (Admin)
- [ ] `PATCH /lab-tests/:id` (Admin)
- [ ] `DELETE /lab-tests/:id` (Admin)
- [ ] `POST /lab-bookings` (Patient)
- [ ] `GET /lab-bookings` (Patient (own) / Lab Technician (own lab))
- [ ] `GET /lab-bookings/:id` (Patient (own) / Lab Technician (own lab))
- [ ] `PATCH /lab-bookings/:id/confirm` (Lab Technician)
- [ ] `PATCH /lab-bookings/:id/cancel` (Patient / Lab Technician)
- [ ] `POST /lab-bookings/:id/result` (Lab Technician (multipart upload))
- [ ] `GET /lab-results` (Patient (own) / Lab Technician (own lab))
- [ ] `GET /lab-results/:id` (Patient (own) / Doctor (with relationship) / Lab Technician (own lab))
- [ ] `GET /lab-results/:id/file` (Same as above; streams the file)
- [ ] `GET /doctors/me/patients/:patientId/lab-results` (Doctor (with relationship))
- [ ] `POST /payments` (Patient)
- [ ] `GET /payments` (Patient (own) / Admin (all, metadata))
- [ ] `GET /payments/:id` (Patient (own) / Admin)
- [ ] `POST /payments/webhook` (Gateway (signature verified))

## Tests

- [ ] Lab technician can only touch bookings of their own lab
- [ ] Upload rejects wrong file types and oversize files; stored under a random key
- [ ] Result files are only reachable through the authenticated endpoint (no public URL)
- [ ] A patient cannot read another patient's result; a doctor without a relationship cannot either
- [ ] Payment webhook with a bad signature is rejected; paying twice does not double-charge
- [ ] Online appointment: meeting link stays hidden until payment is PAID

## Definition of done

- [ ] End to end: search test → book → lab confirms → lab uploads result → patient views it
- [ ] End to end: confirmed online appointment → pay → meeting link released
- [ ] Payments run on the mock provider locally and on the sandbox gateway in staging
- [ ] UC-07, UC-08 (lab side), UC-11, UC-12, UC-13 pass their acceptance check

## Viva / demo preparation

- [ ] Every member can explain: Why a payment provider adapter (gateway availability is a plan constraint; the rest of the app does not care which one is used)
- [ ] Every member can explain: Why the app never stores card data and verifies the gateway callback
- [ ] Every member can explain: Why lab reports are served through an authenticated endpoint instead of public links

## Sign-off

- [ ] Reviewed by another team member: ______
- [ ] `develop` merged into `main`
- [ ] Tagged `v0.5.0`
- [ ] `CHANGELOG.md` updated
