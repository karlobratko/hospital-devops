# Estimation and sprint backlogs

Stories and enablers are estimated in story points on a Fibonacci scale. Points are relative: they compare items with each other and do not stand for hours. The full backlog totals 85 points. At an opening capacity of about 15 points per sprint, four sprints fit roughly 63 of them. The sprint plan below therefore states openly which items are planned and which are at risk.

## Why points and not hours

The hours available in a given fortnight depend on other courses and deadlines, not on the software. A story worth five points stays worth five points in a busy week; fewer points are simply finished. Because the size of the work and the time available are kept apart, velocity (points finished per sprint) becomes a measurement instead of a story about how busy the sprint was.

An estimate considers five factors together: the amount of work, technical complexity, uncertainty, dependencies, and testing effort.

## Scale

| Points | Meaning |
|---|---|
| 1 | Trivial. Understood completely, no new technology, one or two files, barely any test. |
| 2 | Small. Routine work on something that already exists, such as a form field or a list view. |
| 3 | Standard. Several tasks and a few tests, nothing that has not been done before. The anchor lives here. |
| 5 | Substantial. Several layers, a business rule worth testing properly, some awkward cases. |
| 8 | Large or unfamiliar. New technology, security, or something that has to be learned first. |
| 13 | Too big to estimate. Not a real estimate but a signal: the item is split before it enters a sprint. |

Only tasks are not estimated. Tasks inherit their size from their story, and estimating them as well would count the same work twice in sprint totals.

## Anchor

The anchor is **US-03 Register a patient, 3 points**. It has several fields, validation rules, a form and a handful of tests, with no new technology and no business rule beyond validation. Every other item is estimated with the question "is this bigger or smaller than registering a patient?"

## Estimates

| ID | Item | Points | What drives the number |
|---|---|---|---|
| US-01 | Log in and out with role enforcement | 8 | Security, sessions and authorization on every route. The size comes from uncertainty, not volume. |
| US-02 | Register a doctor | 2 | A simple create form on a new but simple entity. |
| US-03 | Register a patient | 3 | The anchor. |
| US-04 | Search and view a patient | 3 | One query and a detail view, plus restricting a doctor to their own patients. |
| US-05 | Schedule an appointment | 5 | Two foreign keys, a business rule with boundary cases, and a concurrency case enforced in the database. |
| US-07 | View my appointments | 3 | Like the anchor, with a filter by the logged-in doctor and a 404 rule. |
| US-06 | Record diagnosis and notes | 3 | Like the anchor, with status transitions and an authorization check. |
| US-09 | Deactivate a patient | 2 | A flag and a refusal rule; the work is mostly in the tests. |
| US-11 | View appointments with filters | 3 | Four filters that combine. |
| US-12 | Update or cancel an appointment | 3 | Re-applies the conflict rule and the status rules on update. |
| US-13 | Read previous medical records | 2 | A read-only view on data that already exists. |
| US-14 | Edit a patient | 2 | Reuses the registration form and validation. |
| US-15 | Available doctors by specialization | 2 | A filtered list. |
| US-16 | View and update a doctor | 2 | Reuses the registration form. |
| US-17 | Deactivate a doctor | 2 | Same pattern as US-09. |
| US-08 | Export authorized data to CSV | 3 | Simple output, but the authorization filter is easy to get wrong. |
| US-10 | Manage user accounts and roles | 5 | Touches the permission model again and needs its own refusal tests. |
| US-18 | Duplicate patient warning | 2 | One extra query in registration. |
| US-19 | Latest diagnosis on the appointment | 1 | One extra field on an existing page. |
| US-20 | Audit log of logins and account changes | 5 | Cross-cutting: every relevant action must write an entry. |
| EN-01 | Application skeleton and conventions | 3 | Routine, but every later item depends on it being right. |
| EN-02 | CI pipeline on a self-hosted runner | 5 | New tooling: runner registration, workflows, database service in CI, branch protection. |
| EN-03 | Container image published to GHCR | 5 | Dockerfile, registry authentication, tagging scheme. |
| EN-04 | Continuous delivery to the prototype environment | 3 | Deploys an image that already exists; most of the hard work is in EN-03. |
| EN-05 | End-to-end tests in the pipeline | 3 | The tests are written with each story; this item makes them run headless in CI. |
| EN-06 | Monitoring with a dashboard | 5 | A new tool to learn plus instrumentation of the application. |

The most expensive story, US-01, has the least visible output. That is uncertainty doing its job. US-04 is one point higher than the estimate shown in the course example, because it also contains the doctor's own-patients restriction (T-04.5).

Totals: Must stories 39 points, Should stories 14, Could stories 8, enablers 24. All together, 85 points.

## Capacity

There is no velocity history before sprint 1, so the first sprint uses an opening capacity of 15 points. This is based on a realistic eight to ten hours of project work per fortnight next to other courses. From sprint 2 onward, planning uses the velocity measured at the previous sprint review, counting only stories accepted as done.

## Sprint backlogs

Each sprint takes items from the top of the product backlog in order, respecting the story dependencies in the [task decomposition](06-task-decomposition.md). Enablers are placed in the sprint in which the course introduces their pipeline stage. Sprints 2 to 4 are a rough outline. They will be replanned with real velocity at each sprint planning.

| Sprint | Goal | Items | Points |
|---|---|---|---|
| 1 | Walking skeleton: a user logs in, and every change is built and tested automatically. | EN-01, EN-02, US-01 | 16 |
| 2 | Patients and doctors are managed end to end, and every merge publishes a container image. | US-02, US-03, US-04, US-09, EN-03 | 15 |
| 3 | Receptionists schedule appointments without double booking, and doctors record their consultations. | US-05, US-07, US-06, US-14, EN-04 | 16 |
| 4 | The calendar is fully manageable, end-to-end tests run in the pipeline, and the application is observable. | US-11, US-12, US-13, EN-05, EN-06 | 16 |

Sprint 1 is planned one point over the opening capacity on purpose. All three items are needed for a walking skeleton. If the sprint runs short, the fallback is to move T-EN2.4 and T-EN2.5 (branch protection and its manual check) into sprint 2.

Two tasks in sprint 2 depend on sprint 3 and move with their blocker. T-04.5 restricts doctors to their own patients and needs appointments. T-09.2 refuses bookings for inactive patients and needs the booking service. Both are done in sprint 3, after T-05.1 and T-05.2. The rest of US-04 and US-09 is demonstrated in sprint 2.

## Scope at risk

The four sprints cover all Must stories and all enablers, 63 of the 85 points. The remaining 22 points are not planned yet.

| Bucket | Items | Points |
|---|---|---|
| Should | US-15, US-16, US-17, US-08, US-10 | 14 |
| Could | US-18, US-19, US-20 | 8 |

This is a Product Owner decision, taken at the sprint 2 review with two sprints of measured velocity. If velocity rises above the opening bid, US-08 (CSV export) and US-10 (user administration) are pulled first, because the scenario names both. Otherwise they compete with the Must stories of sprint 4, and the Could stories are dropped. The project task states that the application is not the focus of the grade, so cutting features is acceptable where cutting the pipeline is not.

## Velocity record

This table is filled in at each sprint review and is the input to the next sprint planning.

| Sprint | Planned points | Accepted points | Notes |
|---|---|---|---|
| 1 | 16 | | |
| 2 | 15 | | |
| 3 | 16 | | |
| 4 | 16 | | |
