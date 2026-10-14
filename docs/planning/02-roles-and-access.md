# Roles and access control

The system has three roles: receptionist, doctor and administrator. Every user account has exactly one role. Access is granted per capability, as listed in the RBAC matrix below. The server enforces every cell of the matrix; the user interface only hides what the server would refuse anyway.

## Roles

### Receptionist (front desk)

The receptionist owns patient records and the appointment calendar. They register, search, view, edit and deactivate patients, and they schedule, update and cancel appointments for any doctor. They can see which doctors are available and what each doctor's specialization is, because they need this to book an appointment. They never see diagnoses or consultation notes.

### Doctor (clinical)

The doctor sees their own appointments and the information of patients who have an appointment with them. After a consultation they record the visit date, a diagnosis and notes. They can read the previous medical records of their own patients and export the appointments and medical data they are authorized to see as a CSV report. A doctor does not manage the calendar, patient registration or other doctors.

### Administrator (system)

The administrator manages the register of doctors and the user accounts of the system. They register, update and deactivate doctors, create user accounts, assign roles and deactivate accounts. They also have full access to patient and appointment administration, so that the front desk can be covered when needed. The administrator is not a clinical role and has no access to medical records.

### Patients are not users

The scenario gives patients no login, no self-booking and no access to their own records. A patient role was considered and ruled out. Patients exist only as data managed by staff.

## RBAC matrix

Each capability has an identifier (C-xx) and refers to the functional requirements it implements. "Own" means the doctor is the doctor on at least one appointment with that patient, or on that appointment.

| ID | Capability | Requirements | Receptionist | Doctor | Administrator |
|---|---|---|---|---|---|
| C-01 | Log in and log out | FR-21, FR-22 | Yes | Yes | Yes |
| C-02 | Register a patient | FR-01 | Yes | No | Yes |
| C-03 | Search and view patients | FR-02, FR-03 | All | Own patients | All |
| C-04 | Edit a patient | FR-04 | Yes | No | Yes |
| C-05 | Deactivate a patient | FR-05 | Yes | No | Yes |
| C-06 | View doctors, specializations and availability | FR-07, FR-10 | Yes | Yes | Yes |
| C-07 | Register, update or deactivate a doctor | FR-06, FR-08, FR-09 | No | No | Yes |
| C-08 | Schedule an appointment | FR-11, FR-12 | Yes | No | Yes |
| C-09 | View appointments | FR-13 | All | Own only | All |
| C-10 | Update or cancel an appointment | FR-14, FR-15 | Yes | No | Yes |
| C-11 | Record diagnosis and visit notes | FR-18 | No | Own appointments | No |
| C-12 | Read a patient's medical history | FR-17, FR-20 | No | Own patients | No |
| C-13 | Export authorized data to CSV | FR-27 | No | Own data | No |
| C-14 | Manage user accounts and roles | FR-26 | No | No | Yes |

## Decisions on cells the scenario leaves open

The scenario does not state every cell. These choices are deliberate and are enforced consistently.

C-10, doctor: No. The scenario gives the calendar to the front desk. A doctor who needs to move or cancel an appointment asks the receptionist. This keeps a single owner for scheduling and a single place where the double-booking rule (FR-12) is applied.

C-12, administrator: No. The administrator is a system role, not a clinical one, and medical data is the most sensitive data in the system. Least privilege means a role gets clinical data only when it needs it to do its job.

C-13, administrator: No. The scenario gives export only to doctors. Allowing an administrator to export would open a path to medical data that C-12 already closes.

C-12, doctor: a doctor reads the full medical history of their own patients, including entries written by other doctors. FR-20 asks for previous records to be available to authorized medical staff, and a partial history would be clinically misleading.

C-11, doctor: medical records are append-only. A doctor creates an entry for their own appointment but cannot edit or delete an existing entry. A correction is made with a new entry. This follows from FR-19 (records are retained) and NFR-10 (no physical deletion).

C-03, C-04 and C-08, administrator: Yes. The scenario says administrators manage doctors and users. Giving them front-desk capabilities as well allows cover for an absent receptionist and does not expose any clinical data.

Deactivated accounts: a deactivated user cannot log in, whatever their role.

## Enforcement

Authorization is checked on the server for every request, as required by NFR-04. An unauthenticated request to a protected route returns 401 or redirects to the login page. An authenticated request for a capability the role does not have returns 403. Requests limited to "own" data are filtered in the query, so a doctor asking for another doctor's appointment receives 404 rather than learning that the appointment exists.

## The matrix as a test plan

Every "No" and every "own" restriction in the matrix becomes an automated authorization test that runs in CI on every pull request. For example, a receptionist calling the endpoint that records a diagnosis must receive 403, and a doctor requesting the appointments of another doctor must receive 404. These tests are listed as test tasks under the corresponding user stories in the [task decomposition](06-task-decomposition.md).
