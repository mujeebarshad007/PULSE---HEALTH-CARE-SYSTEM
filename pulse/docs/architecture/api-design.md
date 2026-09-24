# API Design

Base path: `/api/v1` · Docs: Swagger UI at `/api/docs` · Format: JSON · Auth: httpOnly cookies.

## Conventions

- Plural nouns, lowercase, hyphenated: `/lab-bookings`.
- Ids are UUIDs.
- `GET` reads, `POST` creates or triggers, `PUT` replaces a whole resource, `PATCH` changes part of one, `DELETE` removes.
- State changes are explicit sub-routes: `PATCH /appointments/:id/confirm`.
- Every route is protected unless marked Public.
- Lists are paginated: `page` (default 1), `limit` (default 20, max 100), `sort`.

## Response envelope

Success:

```json
{ "success": true, "data": { }, "meta": { "page": 1, "limit": 20, "total": 134 } }
```

`meta` appears only on lists.

Error:

```json
{ "success": false, "error": { "code": "SLOT_TAKEN", "message": "This slot is no longer available.", "details": [] } }
```

## Status codes

| Code | Use |
| --- | --- |
| 200 / 201 / 204 | Success / created / success without body |
| 400 | Validation failed |
| 401 | Not logged in or token expired |
| 403 | Logged in but not allowed (role, ownership, pending / suspended account) |
| 404 | Not found (also used to avoid revealing that another user's record exists) |
| 409 | Conflict (duplicate email, slot taken, invalid status transition) |
| 413 / 415 | File too large / file type not allowed |
| 429 | Rate limit |

## Error codes

`VALIDATION_FAILED` · `AUTH_INVALID_CREDENTIALS` · `AUTH_ACCOUNT_PENDING` · `AUTH_ACCOUNT_SUSPENDED` · `AUTH_TOKEN_EXPIRED` · `FORBIDDEN` · `NOT_FOUND` · `EMAIL_ALREADY_USED` · `SLOT_TAKEN` · `SLOT_UNAVAILABLE` · `INVALID_STATUS_TRANSITION` · `DOCTOR_NOT_VERIFIED` · `NO_PATIENT_RELATIONSHIP` · `PAYMENT_REQUIRED` · `PAYMENT_FAILED` · `FILE_TYPE_NOT_ALLOWED` · `FILE_TOO_LARGE` · `RESOURCE_IN_USE`

## Search parameters (doctors)

`q`, `specializationId`, `symptomId`, `city`, `area`, `mode` (`PHYSICAL` | `ONLINE`), `minFee`, `maxFee`, `sort` (`fee`, `experience`, `name`), `page`, `limit`.

Symptom search resolves as: symptom → diseases with that symptom → their specializations → verified doctors of those specializations.

## Endpoint index

Generated from the phase plans. The phase README is the detailed source for each group.

### Phase 0: Foundation & Setup

| Method | Path | Access | Use case |
| --- | --- | --- | --- |
| `GET` | `/api/v1/health` | Public | — |

### Phase 1: Authentication & User Accounts

| Method | Path | Access | Use case |
| --- | --- | --- | --- |
| `POST` | `/api/v1/auth/register` | Public | UC-01 |
| `POST` | `/api/v1/auth/login` | Public | UC-02 |
| `POST` | `/api/v1/auth/refresh` | Refresh cookie | UC-02 |
| `POST` | `/api/v1/auth/logout` | Authenticated | UC-02 |
| `GET` | `/api/v1/auth/me` | Authenticated | UC-02 |
| `PATCH` | `/api/v1/auth/change-password` | Authenticated | UC-02 |
| `GET` | `/api/v1/users` | Admin | UC-14 |
| `GET` | `/api/v1/users/:id` | Admin | UC-14 |
| `PATCH` | `/api/v1/users/:id/status` | Admin | UC-14 |

### Phase 2: Profiles & Disease Information

| Method | Path | Access | Use case |
| --- | --- | --- | --- |
| `GET` | `/api/v1/patients/me` | Patient | UC-04 |
| `PUT` | `/api/v1/patients/me` | Patient | UC-04 |
| `GET` | `/api/v1/patients/:id` | Admin | UC-04 |
| `PUT` | `/api/v1/patients/:id` | Admin | UC-04 |
| `GET` | `/api/v1/doctors/me` | Doctor | UC-03 |
| `PUT` | `/api/v1/doctors/me` | Doctor | UC-03 |
| `GET` | `/api/v1/doctors/:id` | Public (verified doctors only) | UC-03 |
| `PUT` | `/api/v1/doctors/:id` | Admin | UC-03 |
| `PATCH` | `/api/v1/doctors/:id/verification` | Admin | UC-14 |
| `GET` | `/api/v1/specializations` | Public | UC-03 |
| `POST` | `/api/v1/specializations` | Admin | UC-15 |
| `PATCH` | `/api/v1/specializations/:id` | Admin | UC-15 |
| `DELETE` | `/api/v1/specializations/:id` | Admin | UC-15 |
| `GET` | `/api/v1/diseases` | Public | UC-15 |
| `GET` | `/api/v1/diseases/:slug` | Public | UC-15 |
| `POST` | `/api/v1/diseases` | Admin | UC-15 |
| `PATCH` | `/api/v1/diseases/:id` | Admin | UC-15 |
| `DELETE` | `/api/v1/diseases/:id` | Admin | UC-15 |
| `GET` | `/api/v1/symptoms` | Public | UC-15 |
| `POST` | `/api/v1/symptoms` | Admin | UC-15 |
| `PATCH` | `/api/v1/symptoms/:id` | Admin | UC-15 |
| `DELETE` | `/api/v1/symptoms/:id` | Admin | UC-15 |

### Phase 3: Doctor Search, Schedules & Appointment Booking

| Method | Path | Access | Use case |
| --- | --- | --- | --- |
| `GET` | `/api/v1/doctors` | Public | UC-05 |
| `GET` | `/api/v1/doctors/:id/slots?date=` | Public | UC-06 |
| `GET` | `/api/v1/schedules/me` | Doctor | UC-10 |
| `PUT` | `/api/v1/schedules/me/availability` | Doctor | UC-10 |
| `POST` | `/api/v1/schedules/me/time-off` | Doctor | UC-10 |
| `DELETE` | `/api/v1/schedules/me/time-off/:id` | Doctor | UC-10 |
| `POST` | `/api/v1/appointments` | Patient | UC-06 |
| `GET` | `/api/v1/appointments` | Patient / Doctor / Admin (scoped) | UC-06 |
| `GET` | `/api/v1/appointments/:id` | Participants, Admin (metadata) | UC-06 |
| `PATCH` | `/api/v1/appointments/:id/confirm` | Doctor | UC-08 |
| `PATCH` | `/api/v1/appointments/:id/reject` | Doctor | UC-08 |
| `PATCH` | `/api/v1/appointments/:id/cancel` | Patient / Doctor | UC-06 |
| `PATCH` | `/api/v1/appointments/:id/complete` | Doctor | UC-08 |

### Phase 4: Online Consultation, Diagnosis & E-Prescriptions

| Method | Path | Access | Use case |
| --- | --- | --- | --- |
| `GET` | `/api/v1/appointments/:id/consultation` | Participants | UC-09 |
| `PUT` | `/api/v1/appointments/:id/consultation` | Doctor (own appointment) | UC-09 |
| `POST` | `/api/v1/appointments/:id/prescription` | Verified Doctor (own appointment) | UC-16 |
| `GET` | `/api/v1/prescriptions` | Patient (own) / Doctor (issued) | UC-16 |
| `GET` | `/api/v1/prescriptions/:id` | Patient (own) / issuing Doctor | UC-16 |
| `GET` | `/api/v1/doctors/me/patients/:patientId/history` | Doctor (with relationship) | UC-09 |

### Phase 5: Payments & Lab Tests

| Method | Path | Access | Use case |
| --- | --- | --- | --- |
| `GET` | `/api/v1/labs` | Public | UC-07 |
| `GET` | `/api/v1/labs/:id` | Public | UC-07 |
| `POST` | `/api/v1/labs` | Admin | UC-07 |
| `PATCH` | `/api/v1/labs/:id` | Admin | UC-07 |
| `PATCH` | `/api/v1/labs/:id/technicians/:userId` | Admin | UC-07 |
| `GET` | `/api/v1/lab-tests` | Public | UC-07 |
| `POST` | `/api/v1/labs/:id/tests` | Admin | UC-07 |
| `PATCH` | `/api/v1/lab-tests/:id` | Admin | UC-07 |
| `DELETE` | `/api/v1/lab-tests/:id` | Admin | UC-07 |
| `POST` | `/api/v1/lab-bookings` | Patient | UC-07 |
| `GET` | `/api/v1/lab-bookings` | Patient (own) / Lab Technician (own lab) | UC-08 |
| `GET` | `/api/v1/lab-bookings/:id` | Patient (own) / Lab Technician (own lab) | UC-08 |
| `PATCH` | `/api/v1/lab-bookings/:id/confirm` | Lab Technician | UC-08 |
| `PATCH` | `/api/v1/lab-bookings/:id/cancel` | Patient / Lab Technician | UC-08 |
| `POST` | `/api/v1/lab-bookings/:id/result` | Lab Technician (multipart upload) | UC-13 |
| `GET` | `/api/v1/lab-results` | Patient (own) / Lab Technician (own lab) | UC-12 |
| `GET` | `/api/v1/lab-results/:id` | Patient (own) / Doctor (with relationship) / Lab Technician (own lab) | UC-12 |
| `GET` | `/api/v1/lab-results/:id/file` | Same as above; streams the file | UC-12 |
| `GET` | `/api/v1/doctors/me/patients/:patientId/lab-results` | Doctor (with relationship) | UC-12 |
| `POST` | `/api/v1/payments` | Patient | UC-11 |
| `GET` | `/api/v1/payments` | Patient (own) / Admin (all, metadata) | UC-11 |
| `GET` | `/api/v1/payments/:id` | Patient (own) / Admin | UC-11 |
| `POST` | `/api/v1/payments/webhook` | Gateway (signature verified) | UC-11 |

### Phase 6: Integration, Testing & Deployment

| Method | Path | Access | Use case |
| --- | --- | --- | --- |
| `GET` | `/api/v1/audit-logs` | Admin | — |

