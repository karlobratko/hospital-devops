# Task decomposition

The backlog is decomposed in three levels: epic, user story and task. An epic groups related stories under a two-word theme and is never implemented or estimated on its own. A user story is the unit of value. It is estimated in story points and demonstrated at the sprint review. A task is one change to the repository: one branch, one pull request, a few hours of work, and a clear end.

```text
Epic
 └── User story (or enabler)
      ├── Task
      ├── Task
      └── Test task
```

The tasks are stack-neutral on purpose. They name what changes (an entity, an endpoint, a page, a test) rather than a framework's file. The technology choices are recorded in the [tech stack](08-tech-stack.md).

## Identifiers

| Level | Format | Example | Becomes |
|---|---|---|---|
| Epic | `E<n>` | E4 | Issue `E4 – Appointment scheduling` |
| User story | `US-<nn>` | US-05 | Issue `US-05 schedule-appointment` |
| Enabler | `EN-<nn>` | EN-02 | Issue `EN-02 ci-pipeline` |
| Task | `T-<story>.<n>` | T-05.3 | Issue `T-05.3 Reject clashing appointment for the same doctor` |
| Enabler task | `T-EN<n>.<n>` | T-EN2.2 | Issue `T-EN2.2 Build and unit-test workflow on pull requests` |

Story identifiers are the ones from the [product backlog](03-product-backlog.md). Test tasks carry a tag in the title: `[TEST][auto]` for a test that runs in CI, `[TEST][manual]` for a checked test case. From Lab 03 each task issue also gets a branch named `<issue-number>-<task-id>-<short-description>`, for example `23-t-05-3-conflict-rule`.

## Enablers

Platform and pipeline work does not fit the user-story template, because no hospital role asks for it. It is still real work that takes time, so it is written as enablers (EN-xx) under epic E6. Enablers are estimated, planned into sprints and tracked on the board exactly like stories. On the project board they have the `user-story` level label plus an `enabler` label, so that the hierarchy stays three levels deep.

| ID | Enabler | Course outcome |
|---|---|---|
| EN-01 | Application skeleton and repository conventions | LO2 |
| EN-02 | CI pipeline on a self-hosted runner: build and test on every pull request | LO4, LO5 |
| EN-03 | Container image built and published to GitHub Container Registry | LO6 |
| EN-04 | Continuous delivery to the prototype environment | LO6 |
| EN-05 | End-to-end tests running in the pipeline | LO7, LO8 |
| EN-06 | Monitoring of application metrics with a dashboard | LO9 |

## Epics

| Epic | Theme | Stories and enablers |
|---|---|---|
| E1 | Authentication and access | US-01, US-10, US-20 |
| E2 | Patient management | US-03, US-04, US-09, US-14, US-18 |
| E3 | Doctor management | US-02, US-15, US-16, US-17 |
| E4 | Appointment scheduling | US-05, US-11, US-12 |
| E5 | Medical records and export | US-07, US-06, US-13, US-08, US-19 |
| E6 | Platform and pipeline | EN-01, EN-02, EN-03, EN-04, EN-05, EN-06 |

US-07 (a doctor's view of their appointments) is filed under E5 rather than E4, because it is the entry point of the doctor's clinical workflow, not part of managing the calendar.

## Dependencies between stories

A story cannot start before its blocking stories are done. This decides the order of the sprints.

| Story | Blocked by | Reason |
|---|---|---|
| EN-01 | none | The starting point |
| EN-02 | EN-01 | CI needs something to build and test |
| EN-03 | EN-02 | The image is built by the pipeline |
| EN-04 | EN-03 | Delivery deploys the published image |
| EN-05 | EN-02, US-01 | The first end-to-end flow is logging in |
| EN-06 | EN-03 | Metrics are collected from the running container |
| US-01 | EN-01 | Needs the application skeleton and database |
| US-02 | US-01 | Registration is an administrator-only action |
| US-03 | US-01 | Registration is a receptionist-only action |
| US-04 | US-03 | There is nothing to search before patients exist |
| US-05 | US-02, US-03 | An appointment needs a doctor and a patient |
| US-07 | US-05 | A doctor's list shows appointments |
| US-06 | US-07 | The outcome is recorded on an opened appointment |
| US-09 | US-03 | Deactivates an existing patient |
| US-11 | US-05 | Lists appointments |
| US-12 | US-05 | Changes appointments |
| US-13 | US-06 | Reads medical records |
| US-14 | US-04 | Edits a patient found by search |
| US-15 | US-02 | Lists doctors |
| US-16 | US-02 | Changes doctors |
| US-17 | US-02 | Deactivates doctors |
| US-08 | US-06 | Exports appointments and medical records |
| US-10 | US-01 | Extends the user model introduced by login |
| US-18 | US-03 | Extends patient registration |
| US-19 | US-06 | Shows the latest medical record |
| US-20 | US-01, US-10 | Logs authentication and account changes |

### Partial dependencies

Some stories have one part that depends on a later story. US-04 lets a doctor search only their own patients, but "own" is defined by appointments, which arrive with US-05. US-09 refuses bookings for inactive patients, which also needs US-05. Rather than delaying the whole story, the dependent part is a separate task blocked by a task of the later story (T-04.5 and T-09.2). The rest of the story can be finished and demonstrated earlier.

### A cycle that was broken

Login (US-01) needs user accounts, and creating user accounts (US-10) needs an administrator who is logged in. Taken literally, each story blocks the other. The cycle is broken by splitting out the one part US-01 really needs: T-01.7 creates the first administrator account from configuration at start-up. US-01 then has no dependency on US-10, and US-10 can wait until sprint 4.

## Tasks for the top of the backlog

The items planned for the first sprints are decomposed below. Lower stories are decomposed during refinement, before they reach sprint planning, as required by the [Definition of Ready](04-definition-of-done.md#definition-of-ready). Their details will change before then anyway.

Kinds: Code, Config, Docs, Test (auto) for an automated test that runs in CI, and Test (manual) for a test case executed and recorded by hand.

### EN-01 Application skeleton and repository conventions

| ID | Task | Kind | Blocked by |
|---|---|---|---|
| T-EN1.1 | Create the application skeleton with a `/health` endpoint | Code | none |
| T-EN1.2 | Add the database to `compose.yaml` and set up schema migrations | Config | T-EN1.1 |
| T-EN1.3 | Write `CONTRIBUTING.md`: branch naming, conventional commits, pull request rules | Docs | none |
| T-EN1.4 | Add the user story and task issue templates | Config | none |
| T-EN1.5 | Seed script for the demonstration dataset used by NFR-01 | Code | T-EN1.2 |
| T-EN1.6 | [TEST][auto] Smoke test: `/health` returns 200 | Test (auto) | T-EN1.1 |

### EN-02 CI pipeline

| ID | Task | Kind | Blocked by |
|---|---|---|---|
| T-EN2.1 | Register a self-hosted GitHub Actions runner | Config | none |
| T-EN2.2 | Workflow that builds and runs unit tests on every pull request to `master` | Config | T-EN2.1, T-EN1.1 |
| T-EN2.3 | Run integration tests against a database service in the workflow | Config | T-EN2.2, T-EN1.2 |
| T-EN2.4 | Branch protection on `master` requiring the CI checks to pass (NFR-06) | Config | T-EN2.2 |
| T-EN2.5 | [TEST][manual] Open a pull request with a failing test and confirm that merging is blocked | Test (manual) | T-EN2.4 |

### US-01 Log in and out (E1)

| ID | Task | Kind | Blocked by |
|---|---|---|---|
| T-01.1 | User account entity and migration: username, password hash, role, active flag | Code | T-EN1.2 |
| T-01.2 | Password hashing with bcrypt, cost factor 12 (NFR-03) | Code | T-01.1 |
| T-01.3 | Login and logout endpoints with session handling | Code | T-01.2 |
| T-01.4 | Authentication guard on protected routes: 401 or redirect to login | Code | T-01.3 |
| T-01.5 | Role-based authorization mapping roles to capabilities from the RBAC matrix: 403 | Code | T-01.4 |
| T-01.6 | Login page and logout action in the interface | Code | T-01.3 |
| T-01.7 | Create the first administrator account from configuration at start-up (NFR-11) | Code | T-01.2 |
| T-01.8 | [TEST][auto] Unit tests: hashing, credential check, deactivated account refused | Test (auto) | T-01.2 |
| T-01.9 | [TEST][auto] Integration tests: 401 when unauthenticated, 403 for the wrong role | Test (auto) | T-01.5 |
| T-01.10 | [TEST][auto] End-to-end test: log in, reach the role's home page, log out | Test (auto) | T-01.6 |

### US-02 Register a doctor (E3)

| ID | Task | Kind | Blocked by |
|---|---|---|---|
| T-02.1 | Doctor entity and migration: name, contact details, specialization, active flag | Code | T-EN1.2 |
| T-02.2 | Endpoint to register a doctor, with validation, administrator only | Code | T-02.1, T-01.5 |
| T-02.3 | Doctor registration form and list of doctors | Code | T-02.2 |
| T-02.4 | [TEST][auto] Integration tests: valid save, missing fields refused, 403 for other roles | Test (auto) | T-02.2 |
| T-02.5 | [TEST][manual] Validation messages and required-field marking on the form | Test (manual) | T-02.3 |

### US-03 Register a patient (E2)

| ID | Task | Kind | Blocked by |
|---|---|---|---|
| T-03.1 | Patient entity and migration: name, date of birth, contact, address, active flag | Code | T-EN1.2 |
| T-03.2 | Endpoint to register a patient, with validation: required fields, date of birth not in the future | Code | T-03.1, T-01.5 |
| T-03.3 | Patient registration form | Code | T-03.2 |
| T-03.4 | [TEST][auto] Unit tests for the validation rules | Test (auto) | T-03.2 |
| T-03.5 | [TEST][auto] Integration tests: valid save, 403 for a doctor | Test (auto) | T-03.2 |
| T-03.6 | [TEST][manual] Form wording and error display | Test (manual) | T-03.3 |

### US-04 Search and view a patient (E2)

| ID | Task | Kind | Blocked by |
|---|---|---|---|
| T-04.1 | Search endpoint by name, date of birth or contact, inactive patients excluded by default | Code | T-03.1 |
| T-04.2 | Patient detail endpoint | Code | T-03.1 |
| T-04.3 | Search page with results and a detail view | Code | T-04.1, T-04.2 |
| T-04.4 | [TEST][auto] Integration tests: matching rules, inactive patients excluded | Test (auto) | T-04.1 |
| T-04.5 | Restrict a doctor's search and detail view to their own patients, 404 otherwise | Code | T-04.2, T-05.1 |
| T-04.6 | [TEST][auto] End-to-end test: register a patient, then find and open the record | Test (auto) | T-04.3, T-03.3 |

### US-05 Schedule an appointment (E4)

| ID | Task | Kind | Blocked by |
|---|---|---|---|
| T-05.1 | Appointment entity and migration: patient, doctor, slot start, reason, status | Code | T-02.1, T-03.1 |
| T-05.2 | Service and endpoint to create an appointment, receptionist and administrator only | Code | T-05.1, T-01.5 |
| T-05.3 | Conflict rule: reject a second `scheduled` appointment for the same doctor and slot, enforced by a unique partial index in the database | Code | T-05.1 |
| T-05.4 | Booking form with patient and doctor pickers and a slot selector | Code | T-05.2 |
| T-05.5 | [TEST][auto] Unit tests for the conflict rule: same slot, cancelled slot, adjacent slot, slot alignment | Test (auto) | T-05.3 |
| T-05.6 | [TEST][auto] Integration test: two concurrent bookings for the same slot, exactly one succeeds | Test (auto) | T-05.3 |
| T-05.7 | [TEST][auto] End-to-end test: book an appointment, then try a clashing booking and expect refusal | Test (auto) | T-05.4 |
| T-05.8 | [TEST][manual] Validation wording and the empty state when no slot is free | Test (manual) | T-05.4 |

The conflict rule is enforced in the database and not only checked in code. Two receptionists submitting at the same moment would both pass an "is this slot free?" check before either insert lands. A unique index on doctor and slot start, limited to `scheduled` appointments, makes the second insert fail, and T-05.6 proves it.

### US-07 View my appointments (E5)

| ID | Task | Kind | Blocked by |
|---|---|---|---|
| T-07.1 | Endpoint returning only the logged-in doctor's appointments | Code | T-05.1, T-01.5 |
| T-07.2 | Patient details for a doctor's own patients only, 404 otherwise | Code | T-07.1, T-04.5 |
| T-07.3 | "My appointments" page grouped by day | Code | T-07.1 |
| T-07.4 | [TEST][auto] Integration tests: own appointments only, another doctor's appointment returns 404 | Test (auto) | T-07.2 |
| T-07.5 | [TEST][auto] End-to-end test: a doctor logs in and sees a booked appointment | Test (auto) | T-07.3 |

### US-06 Record diagnosis and notes (E5)

| ID | Task | Kind | Blocked by |
|---|---|---|---|
| T-06.1 | Medical record entity and migration, append-only | Code | T-05.1 |
| T-06.2 | Service to record an outcome: creates the record, marks the appointment `completed`, refuses cancelled appointments and other doctors' appointments | Code | T-06.1, T-07.1 |
| T-06.3 | Consultation form on the appointment page | Code | T-06.2, T-07.3 |
| T-06.4 | [TEST][auto] Unit tests for appointment status transitions (FR-16) | Test (auto) | T-06.2 |
| T-06.5 | [TEST][auto] Integration tests: 404 for another doctor, 403 for other roles, edit and delete refused | Test (auto) | T-06.2 |
| T-06.6 | [TEST][auto] End-to-end test: record an outcome, appointment shows `completed` | Test (auto) | T-06.3 |

### US-09 Deactivate a patient (E2)

| ID | Task | Kind | Blocked by |
|---|---|---|---|
| T-09.1 | Endpoint to deactivate a patient (sets the active flag, deletes nothing) | Code | T-03.1 |
| T-09.2 | Refuse bookings for inactive patients | Code | T-09.1, T-05.2 |
| T-09.3 | Deactivate action on the patient page and an "include inactive" search option | Code | T-09.1, T-04.3 |
| T-09.4 | [TEST][auto] Integration tests: history preserved after deactivation, booking refused | Test (auto) | T-09.2 |
| T-09.5 | [TEST][manual] Confirmation dialog wording and the inactive marker in search results | Test (manual) | T-09.3 |

## Testing approach

Every story has at least one automated test task, as required by the [Definition of Done](04-definition-of-done.md). The kind of test follows from what could break.

Business rules and authorization are covered by automated unit and integration tests, because their failures are silent. A broken double-booking check or a missing permission check would not be noticed by looking at the screen. Every refused cell of the RBAC matrix appears as an integration test.

Main user flows (log in, register a patient, book an appointment, record an outcome) are covered by automated end-to-end tests that drive the browser. They run in the pipeline once EN-05 is done; until then they run locally.

Look and feel (error wording, empty states, confirmation dialogs) is covered by short manual test cases, executed before the story is closed and recorded in its issue. These failures are noticed immediately by anyone using the page, so automating them would cost more than it saves.

## Remaining stories

US-11, US-12, US-13, US-14, US-15, US-16, US-17, US-08, US-10, US-18, US-19, US-20 and enablers EN-03 to EN-06 are placed under their epics and have their story-level dependencies recorded above. They are decomposed into tasks during backlog refinement before the sprint that pulls them. The enablers are decomposed when the course lab that introduces each pipeline stage defines how it is built.
