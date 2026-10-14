# Hospital Management — DevOps Project

A small hospital administration web application, built as the vehicle for a complete GitHub-based DevOps pipeline. The application manages patients, doctors, appointments and basic medical records, with authentication and role-based access control.

The application is deliberately simple. The deliverable is the process around it: every change travels from a requirement to a GitHub issue, a branch, a pull request, an automated build and test, and a published container image in GitHub Container Registry.

Course: Applied DevOps, Graduate Study in Software Engineering, Algebra Bernays University.

## Project board

The work is tracked on the GitHub Project: _link added in Lab 03_.

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
