# Hospital Management — DevOps Project

A small hospital administration web application, built as the vehicle for a complete GitHub-based DevOps pipeline. The application manages patients, doctors, appointments and basic medical records, with authentication and role-based access control.

The application is deliberately simple. The deliverable is the process around it: every change travels from a requirement to a GitHub issue, a branch, a pull request, an automated build and test, and a published container image in GitHub Container Registry.

Course: Applied DevOps, Graduate Study in Software Engineering, Algebra Bernays University.

## Project board

The work is tracked on the GitHub Project: https://github.com/users/karlobratko/projects/4.

## 1. Project overview

Hospital staff use the application to maintain patient information, a register of doctors and their specializations, and an appointment calendar. Doctors record a diagnosis and notes after each consultation and can export their authorized data as a CSV report.

Three staff roles use the system: receptionists, doctors and administrators. Patients are data in the system, not users of it.

The DevOps workflow this repository demonstrates is:

```text
requirement → GitHub issue → project task → branch → pull request → build → test → publish
```

## 2. Requirement analysis

The scenario defines 27 functional requirements in six groups (patients, doctors, appointments, medical records, authentication, and access and export) and four non-functional requirements. Seven more non-functional requirements follow from the DevOps process itself. Every requirement has an identifier (FR-xx, NFR-xx) that issues and tests refer to.

The one business rule in the scenario is that a doctor cannot have two appointments at the same date and time. Deactivating a patient or a doctor never deletes their history.

Full analysis: [docs/planning/01-requirements.md](docs/planning/01-requirements.md)

## 3. Roles and access control

Three roles use the system. Receptionists own patient records and the appointment calendar. Doctors see their own appointments and patients, record diagnoses and notes, and export their data to CSV. Administrators manage doctors and user accounts and can cover front-desk work, but have no access to medical records. Patients are not users.

Access is defined per capability in an RBAC matrix of 14 capabilities. Cells the scenario leaves open are decided and justified in the document. Every refusal in the matrix is enforced on the server and covered by an automated test.

Roles and RBAC matrix: [docs/planning/02-roles-and-access.md](docs/planning/02-roles-and-access.md)

## 4. Product backlog

The backlog holds 20 user stories in one ordered list: 12 Must, 5 Should and 3 Could. Seventeen come directly from the scenario, and three are invented extras, one per role. The order follows dependency first, then risk, then value. That puts login (US-01) at the top, followed by the doctor and patient registration that scheduling depends on. Acceptance criteria are written for the top six stories, and every functional requirement maps to at least one story.

A story enters a sprint only when it meets the Definition of Ready, and it counts as finished only when it meets the Definition of Done. The Definition of Done requires a reviewed pull request into `master`, passing tests in CI, and a published container image.

Ordered backlog: [docs/planning/03-product-backlog.md](docs/planning/03-product-backlog.md)

Definition of Ready and Done: [docs/planning/04-definition-of-done.md](docs/planning/04-definition-of-done.md)

## 5. Process and ceremonies

The project uses Scrum with four two-week sprints, each with a one-sentence goal. Each sprint delivers application features and a new pipeline stage: CI in sprint 1, a published container image in sprint 2, continuous delivery in sprint 3, and end-to-end tests and monitoring in sprint 4. Planning, review and retrospective are held each sprint and recorded in `docs/sprints/`. Because there is one developer, a dated work log replaces the daily scrum. Backlog refinement is continuous and is triggered by six named events.

Sprints, ceremonies and refinement triggers: [docs/planning/05-process.md](docs/planning/05-process.md)

## 6. Task decomposition

The backlog is organised into six epics. Five cover the application: authentication, patients, doctors, appointments, and medical records with export. The sixth, E6 Platform and pipeline, holds six enablers, one per pipeline stage. These run from the application skeleton and CI through to monitoring.

The stories and enablers planned for the first sprints are broken into tasks. Each task is one branch and one pull request. Every story has at least one automated test task, and is marked `[TEST][auto]` or `[TEST][manual]`. Dependencies are recorded between stories and between tasks. One cycle between login and user administration is broken by seeding the first administrator from configuration.

Epics, stories, tasks and dependencies: [docs/planning/06-task-decomposition.md](docs/planning/06-task-decomposition.md)

## 7. Estimation and sprint backlogs

Stories and enablers are estimated in relative story points on a Fibonacci scale, and each value has a stated meaning. US-03, Register a patient, is the anchor at 3 points. The backlog totals 85 points. Four sprints at an opening capacity of about 15 points cover every Must story and every enabler, 63 points in all. The remaining 22 points of Should and Could stories are named as scope at risk, to be decided once velocity has been measured.

Scale, estimates and sprint backlogs: [docs/planning/07-estimation-and-sprints.md](docs/planning/07-estimation-and-sprints.md)

## 8. Activity diagram

The main flow is scheduling an appointment and recording the consultation. It is modelled as a UML activity diagram with three swimlanes: receptionist, system and doctor. The diagram shows the role permission check, the check that patient and doctor are active, and the double-booking rule. It also shows the database-level guard that catches a concurrent booking for the same slot. Every refusal leads back to the receptionist with a reason, apart from a permission refusal, which ends the flow.

Diagram source: [docs/diagrams/activity-appointment.drawio](docs/diagrams/activity-appointment.drawio)

## 9. Tools and tech stack

The application uses Kotlin 2.4 on JDK 25, with Ktor 3.5 and server-rendered kotlinx.html pages enhanced with HTMX. Data lives in PostgreSQL 18, accessed without blocking through Exposed over R2DBC, with Flyway migrations. Tests use kotlin.test and Kotest, with Testcontainers for integration tests and Playwright for end-to-end tests. The application is packaged as a Docker image and published to GitHub Container Registry. GitHub Actions on a self-hosted runner builds, tests and publishes it, and Prometheus and Grafana monitor it.

Full stack with justification: [docs/planning/08-tech-stack.md](docs/planning/08-tech-stack.md)

## Repository layout

```text
README.md                  this page
CONTRIBUTING.md            branch naming and pull request rules (Lab 03)
.github/ISSUE_TEMPLATE/    user story and task templates (Lab 03)
docs/planning/             requirements, roles, backlog, process, decomposition, estimation, stack
docs/sprints/              one record per sprint: goal, work log, review, retrospective
docs/diagrams/             UML diagrams as .drawio source files
docs/decisions/            written analyses: branching, CI, test tooling, monitoring
```
