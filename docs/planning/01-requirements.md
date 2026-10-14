
# Requirement analysis

This document lists the functional and non-functional requirements extracted from the project scenario (ProjectTask.pdf, section 2). Every requirement has a stable identifier. User stories, issues, tests and pull requests refer back to these identifiers, so the identifiers never change once published. A requirement that is dropped is marked as withdrawn instead of being renumbered.

## How the scenario was analysed

The scenario was read sentence by sentence and each phrase was classified into one of four kinds:

| Kind of phrase | Example from the scenario | Where it goes |
|---|---|---|
| Role | "a receptionist" | [Roles and access control](02-roles-and-access.md) |
| Action | "register a new patient" | Functional requirement, later a user story |
| Entity | "patient", "name", "date of birth" | Domain model and database schema |
| Constraint | "within two seconds" | Non-functional requirement with a measurable threshold |

Functional requirements become user stories in the [product backlog](03-product-backlog.md). Non-functional requirements become acceptance criteria, automated tests and pipeline checks.

## Domain entities

The scenario names five entities. They are listed here because several requirements depend on their attributes.

| Entity | Attributes stated in the scenario | Notes |
|---|---|---|
| Patient | name, date of birth, contact information, address, active flag | Deactivated, never deleted |
| Doctor | name, contact details, medical specialization, active flag | Deactivated, never deleted |
| Appointment | patient, doctor, date, time, reason, status | Status is `scheduled`, `completed` or `cancelled` |
| Medical record | patient, doctor, appointment, visit date, diagnosis, notes | Retained permanently |
| User account | username, password hash, role, active flag | Roles: receptionist, doctor, administrator |

A doctor who uses the system has both a Doctor entry and a User account, linked one to one.

## 2.1 Functional requirements

### Patient management

| ID | Requirement | Primary role |
|---|---|---|
| FR-01 | The system shall allow registering a new patient with name, date of birth, contact information (phone and/or e-mail) and address. | Receptionist |
| FR-02 | The system shall allow searching existing patients by name, date of birth or contact information. | Receptionist |
| FR-03 | The system shall display the full details of a selected patient. | Receptionist |
| FR-04 | The system shall allow editing a patient's information, for example when contact details change. | Receptionist |
| FR-05 | The system shall allow deactivating a patient record. A deactivated patient cannot be booked for new appointments, but the record and all associated appointments and medical records are preserved. | Receptionist |

### Doctor management

| ID | Requirement | Primary role |
|---|---|---|
| FR-06 | The system shall allow registering a doctor with name, contact details and medical specialization. | Administrator |
| FR-07 | The system shall allow viewing the list of doctors and the details of a selected doctor. | Administrator |
| FR-08 | The system shall allow updating a doctor's information. | Administrator |
| FR-09 | The system shall allow deactivating a doctor who is no longer available. A deactivated doctor cannot be booked for new appointments, and their history is preserved. | Administrator |
| FR-10 | The system shall show which doctors are available to provide appointments and the specialization of each, with filtering by specialization. | Receptionist |

### Appointment management

| ID | Requirement | Primary role |
|---|---|---|
| FR-11 | The system shall allow scheduling an appointment by selecting a patient and a doctor and specifying date, time and reason for the visit. A new appointment has status `scheduled`. | Receptionist |
| FR-12 | The system shall reject an appointment that would assign a doctor two appointments at the same date and time, and shall explain the reason to the user. | System rule |
| FR-13 | The system shall allow viewing existing appointments, filtered by date, doctor, patient and status. | Receptionist |
| FR-14 | The system shall allow updating an appointment's date, time, doctor or reason. The double-booking rule (FR-12) is checked again on update. | Receptionist |
| FR-15 | The system shall allow cancelling an appointment. A cancelled appointment keeps its data and has status `cancelled`. | Receptionist |
| FR-16 | An appointment's status shall move only from `scheduled` to `completed` or from `scheduled` to `cancelled`. | System rule |

### Medical records

| ID | Requirement | Primary role |
|---|---|---|
| FR-17 | The system shall allow a doctor to view the information of patients who have appointments with them. | Doctor |
| FR-18 | After an appointment, the system shall allow the doctor to record the visit date, a diagnosis and notes describing the outcome of the consultation. Recording the visit marks the appointment `completed`. | Doctor |
| FR-19 | The system shall retain all medical record entries permanently. | System rule |
| FR-20 | The system shall allow authorized medical staff to view a patient's previous medical records. | Doctor |

### Authentication

| ID | Requirement | Primary role |
|---|---|---|
| FR-21 | The system shall allow users to log in with a username and password. | All |
| FR-22 | The system shall allow a logged-in user to log out. | All |
| FR-23 | The system shall require authentication before any protected functionality is reached. | System rule |
| FR-24 | The system shall distinguish three roles: receptionist, doctor and administrator. | System rule |

### Access control and export

| ID | Requirement | Primary role |
|---|---|---|
| FR-25 | The system shall restrict functionality according to the user's role, as defined in the [RBAC matrix](02-roles-and-access.md). | System rule |
| FR-26 | The system shall allow an administrator to create user accounts, assign a role, and deactivate accounts. | Administrator |
| FR-27 | The system shall allow a doctor to export the appointments and medical data they are authorized to view into a CSV report. | Doctor |

### Interpretations and assumptions

The scenario leaves some details open. The choices below are deliberate and are enforced consistently.

An appointment occupies a fixed 30-minute slot that starts on the hour or half hour. "The same date and time" in FR-12 therefore means the same doctor and the same slot start. This makes the rule unambiguous and testable at its boundaries.

Patients are data, not users. The scenario gives patients no login, no self-booking and no access to their own records, so there is no patient role. This was considered and ruled out.

A medical record is always tied to an appointment of the recording doctor. A doctor cannot create a record for a patient who has no appointment with them.

## 2.2 Non-functional requirements

Every non-functional requirement has a measurable threshold or a named mechanism, and a stated way of verifying it. The first four are stated in the scenario. The rest follow from the requirement that the application is developed, tested, built, delivered, deployed, verified and monitored through a DevOps process.

### Stated in the scenario

| ID | Category | Requirement | Verified by |
|---|---|---|---|
| NFR-01 | Performance | 95% of HTTP requests complete in under 2 seconds with 20 concurrent virtual users for 5 minutes, against the seeded demo dataset (500 patients, 50 doctors, 2,000 appointments). | Load test (k6) run against the deployed container before a release |
| NFR-02 | Availability | The application container has a health check endpoint (`/health`) and a restart policy. After the application process is killed, the service is responding again within 30 seconds without manual action. | Restart policy and health check declared in `compose.yaml`; manual kill test documented |
| NFR-03 | Security, credential storage | Passwords are stored only as salted hashes using bcrypt with a cost factor of at least 12. Passwords never appear in logs, responses or the repository. | Unit test on the hashing function; database inspection |
| NFR-04 | Security, access control | Every protected route returns 401 (or redirects to login) for an unauthenticated request and 403 for an authenticated user whose role lacks permission. Checks are enforced on the server, never only in the user interface. | Automated authorization tests derived from each "No" cell of the RBAC matrix |

### Implied by the DevOps process

| ID | Category | Requirement | Verified by |
|---|---|---|---|
| NFR-05 | Deployability | A merge to `main` produces a container image published to GitHub Container Registry, tagged with the version and the commit SHA, without manual steps. | GitHub Actions run history; image list in GHCR |
| NFR-06 | Testability | Unit, integration and end-to-end tests run automatically on every pull request. A pull request with a failing check cannot be merged. | Branch protection rule on `main`; Actions checks on pull requests |
| NFR-07 | Observability | The application exposes request count, request latency and error rate as metrics, and they are visible on a dashboard. | Metrics endpoint and dashboard screenshot in the monitoring documentation |
| NFR-08 | Maintainability | The branching strategy and the versioning scheme are documented, and every change reaches `main` through a reviewed pull request linked to an issue. | Documentation in `docs/decisions/`; pull request history |
| NFR-09 | Portability | The full stack (application and database) starts locally with a single `docker compose up`. | Fresh clone and start, documented in the README |
| NFR-10 | Data integrity | Patients, doctors, appointments and medical records are never physically deleted through the application. Deactivation and cancellation preserve all historical data. | Integration tests on deactivate and cancel operations |
| NFR-11 | Configuration and secrets | No credentials or secrets are committed to the repository. Configuration is supplied through environment variables and GitHub Actions secrets. | Secret scanning enabled on the repository; review of the compose and workflow files |

## Traceability

Each functional requirement is covered by at least one user story in the [product backlog](03-product-backlog.md). Each non-functional requirement appears either as an acceptance criterion, as a test task, or as a pipeline check. The mapping from requirements to stories is maintained in the backlog document.
