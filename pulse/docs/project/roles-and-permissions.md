# Roles & Permissions

Legend: ✅ allowed · 🔒 own records only · 🤝 only with a doctor-patient relationship · ➖ not applicable / not allowed

A **relationship** exists when the doctor has at least one CONFIRMED or COMPLETED appointment with the patient.

| Capability | Visitor | Patient | Doctor | Lab Tech | Admin |
| --- | :---: | :---: | :---: | :---: | :---: |
| Register | ✅ | ➖ | ➖ | ➖ | ➖ |
| Log in / log out / change password | ➖ | ✅ | ✅ | ✅ | ✅ |
| Browse verified doctors and their profiles | ✅ | ✅ | ✅ | ✅ | ✅ |
| Search doctors (specialization, symptom, location) | ✅ | ✅ | ✅ | ✅ | ✅ |
| Browse disease information and symptoms | ✅ | ✅ | ✅ | ✅ | ✅ |
| Browse labs and search lab tests | ✅ | ✅ | ✅ | ✅ | ✅ |
| Edit own profile | ➖ | 🔒 | 🔒 | 🔒 | ✅ any |
| Approve / suspend accounts | ➖ | ➖ | ➖ | ➖ | ✅ |
| Verify a doctor | ➖ | ➖ | ➖ | ➖ | ✅ |
| Manage specializations, symptoms, diseases | ➖ | ➖ | ➖ | ➖ | ✅ |
| Manage labs, lab tests, technician assignment | ➖ | ➖ | ➖ | ➖ | ✅ |
| Define / update schedule | ➖ | ➖ | 🔒 | ➖ | ➖ |
| See free slots of a doctor | ✅ | ✅ | ✅ | ✅ | ✅ |
| Book an appointment | ➖ | ✅ | ➖ | ➖ | ➖ |
| Confirm / reject / complete an appointment | ➖ | ➖ | 🔒 | ➖ | ➖ |
| Cancel an appointment | ➖ | 🔒 | 🔒 | ➖ | ➖ |
| View appointment details | ➖ | 🔒 | 🔒 | ➖ | metadata only |
| Write diagnosis / consultation notes | ➖ | ➖ | 🔒 | ➖ | ➖ |
| Issue prescription | ➖ | ➖ | 🔒 verified only | ➖ | ➖ |
| View diagnosis and prescriptions | ➖ | 🔒 | 🔒 issued by them | ➖ | ➖ |
| View a patient's appointment history | ➖ | 🔒 | 🤝 | ➖ | ➖ |
| Book a lab test | ➖ | ✅ | ➖ | ➖ | ➖ |
| Confirm / complete a lab booking | ➖ | ➖ | ➖ | 🔒 own lab | ➖ |
| Upload lab result | ➖ | ➖ | ➖ | 🔒 own lab | ➖ |
| View lab results | ➖ | 🔒 | 🤝 | 🔒 own lab | ➖ |
| Make a payment | ➖ | 🔒 | ➖ | ➖ | ➖ |
| View payment history | ➖ | 🔒 | ➖ | ➖ | metadata only |
| View audit logs | ➖ | ➖ | ➖ | ➖ | ✅ |

## Account status effects

| Status | Can log in | Visible on public pages |
| --- | :---: | :---: |
| ACTIVE | ✅ | ✅ (doctors only if also verified) |
| PENDING_VERIFICATION | ➖ | ➖ |
| SUSPENDED | ➖ | ➖ |

## Design principles

1. **Least privilege:** admins manage the platform, not patients' medical content.
2. **Deny by default:** every route needs authentication unless marked public.
3. **Ownership is checked in the service layer,** not only by role.
4. **The UI mirrors permissions but never replaces them.**
