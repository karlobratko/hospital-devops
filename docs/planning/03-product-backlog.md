# Product backlog

The product backlog is a single ordered list of user stories. The order is the Product Owner's decision about what is built next. Position 1 is pulled into a sprint first. Each story has a stable identifier (US-xx) that does not change when the order changes, so issues, branches and tests keep pointing to the same story.

Stories are written with the standard template: *As a [role], I want [feature], so that [benefit].* Every story names one of the three roles from [Roles and access control](02-roles-and-access.md), or "staff member" when it applies to all three.

Technical enabler work (repository conventions, containerisation, CI, CD, monitoring) is not written as user stories here. It is added as epic E6 Platform and pipeline during task decomposition in Lab 02 and is estimated and scheduled alongside the stories.

## How the order was decided

Stories were first sorted into MoSCoW buckets (Must, Should, Could), then ordered inside the backlog with three tie-breakers, applied in this order.

Dependency comes first. Nothing can be demonstrated before login exists, and no appointment can be booked before doctors and patients exist.

Risk comes second. Work that is least understood is done early, while there is still time to be wrong about it. Authentication and the double-booking rule carry the most risk.

Value comes third. Among stories that are equally unblocked and equally risky, the one the hospital would miss most comes first.

The first sprint aims for a thin vertical slice: one flow that logs in, writes to the database and runs in a container is worth more early on than any complete module.

## Ordered backlog

| # | ID | User story | MoSCoW | Requirements |
|---|---|---|---|---|
| 1 | US-01 | As a staff member, I want to log in and out with my username and password, so that I reach only the functionality my role permits. | Must | FR-21, FR-22, FR-23, FR-24, FR-25 |
| 2 | US-02 | As an administrator, I want to register a doctor with their contact details and specialization, so that the doctor can be booked for appointments. | Must | FR-06 |
| 3 | US-03 | As a receptionist, I want to register a new patient with their name, date of birth, contact information and address, so that the patient can be booked for an appointment. | Must | FR-01 |
| 4 | US-04 | As a receptionist, I want to search for an existing patient and view their details, so that I can find the right record without registering the patient twice. | Must | FR-02, FR-03 |
| 5 | US-05 | As a receptionist, I want to schedule an appointment for a patient with a doctor at a date and time, with a reason for the visit, so that the patient is seen by the right doctor without clashing with another booking. | Must | FR-11, FR-12 |
| 6 | US-07 | As a doctor, I want to see my scheduled appointments and the details of the patients I am seeing, so that I can prepare for each consultation. | Must | FR-13, FR-17 |
| 7 | US-06 | As a doctor, I want to record the visit date, a diagnosis and notes after an appointment, so that the outcome of the consultation is preserved for future care. | Must | FR-16, FR-18, FR-19 |
| 8 | US-09 | As a receptionist, I want to deactivate a patient record that is no longer in use, so that it cannot be booked again while its history is preserved. | Must | FR-05 |
| 9 | US-11 | As a receptionist, I want to view appointments filtered by date, doctor, patient and status, so that I can manage the calendar and answer patients' questions. | Must | FR-13 |
| 10 | US-12 | As a receptionist, I want to update or cancel an existing appointment, so that the calendar reflects changes requested by patients or doctors. | Must | FR-14, FR-15, FR-16 |
| 11 | US-13 | As a doctor, I want to read the previous medical records of a patient I am seeing, so that I know their history before the consultation. | Must | FR-17, FR-19, FR-20 |
| 12 | US-14 | As a receptionist, I want to edit a patient's information, so that their contact details stay correct when they change. | Must | FR-04 |
| 13 | US-15 | As a receptionist, I want to see which doctors are available and filter them by specialization, so that I book the patient with a suitable doctor. | Should | FR-07, FR-10 |
| 14 | US-16 | As an administrator, I want to view and update a doctor's information, so that the register of doctors stays accurate. | Should | FR-07, FR-08 |
| 15 | US-17 | As an administrator, I want to deactivate a doctor who is no longer available, so that they cannot be booked while their past appointments and records are preserved. | Should | FR-09 |
| 16 | US-08 | As a doctor, I want to export the appointments and medical data I am authorized to see into a CSV report, so that I can analyse or share my work outside the application. | Should | FR-27 |
| 17 | US-10 | As an administrator, I want to create user accounts, assign roles and deactivate accounts, so that every staff member has exactly the access their job requires. | Should | FR-26 |
| 18 | US-18 | As a receptionist, I want to be warned when I register a patient whose name and date of birth match an existing patient, so that duplicate records are avoided. | Could | invented |
| 19 | US-19 | As a doctor, I want to see the patient's most recent diagnosis when I open an appointment, so that I can prepare for the consultation without searching the full history. | Could | invented |
| 20 | US-20 | As an administrator, I want to see a log of logins and changes to user accounts, so that I can investigate suspected misuse of the system. | Could | invented |

The identifiers are not in backlog order on purpose. US-01 to US-10 match the numbering used in the course examples, and stories found later received the next free number. The backlog position changes during refinement; the identifier never does.

## Invented stories

The lab asks for one additional story per role beyond what the scenario states. US-18 (receptionist), US-19 (doctor) and US-20 (administrator) are those stories. They are not backed by a functional requirement, so they sit in the Could bucket at the bottom of the backlog. They will be built only if the Must and Should stories are finished, and if they are built they are added as FR-28 to FR-30 in the [requirements](01-requirements.md).

US-20 also supports NFR-04 and NFR-07, since an audit log of authentication events is useful evidence for both access control and observability.

## Justification of the top three

US-01 is first because every other story depends on it: no protected page can be demonstrated, and no authorization test can be written, until a user can log in with a role. It is also the riskiest story, since it touches sessions, password hashing and role enforcement on every route.

US-02 and US-03 come next because US-05, the only story with a real business rule, cannot start until both doctors and patients exist. US-02 is placed before US-03 because a doctor is a simpler entity and is the first data an administrator enters into a fresh system.

US-05 then follows at position 5, after US-04 search, since a receptionist must find the patient before booking. The doctor's view of appointments (US-07) is placed before recording a diagnosis (US-06) because a doctor must open an appointment before writing its outcome.

## Acceptance criteria

Acceptance criteria are written in Given / When / Then form, because that form converts directly into an automated test. Criteria are given here for the top of the backlog. The remaining stories receive their criteria during refinement, before they are pulled into a sprint, as required by the [Definition of Ready](04-definition-of-done.md#definition-of-ready).

### US-01 Log in and out

- Given an active user with a correct username and password, when they log in, then they reach the home page for their role.
- Given a wrong password or an unknown username, when the user logs in, then the login is refused with the same generic message in both cases.
- Given a deactivated user account, when the user logs in with correct credentials, then the login is refused.
- Given an unauthenticated visitor, when they request any protected page, then they are redirected to the login page (or receive 401 from the API).
- Given a logged-in receptionist, when they request a page that only a doctor may use, then they receive 403.
- Given a logged-in user, when they log out, then their session ends and protected pages require a new login.

### US-02 Register a doctor

- Given an administrator, when they submit a doctor with name, contact details and a specialization, then the doctor is saved as active and appears in the list of doctors.
- Given a submission without a name or a specialization, when the administrator saves, then the doctor is not saved and the missing fields are shown.
- Given a receptionist or a doctor, when they try to register a doctor, then they receive 403.

### US-03 Register a patient

- Given a receptionist, when they submit a patient with name, date of birth, contact information and address, then the patient is saved as active and can be found by search.
- Given a date of birth in the future, when the receptionist saves, then the patient is not saved and the error is explained.
- Given a submission without a name or a date of birth, when the receptionist saves, then the patient is not saved and the missing fields are shown.
- Given a doctor, when they try to register a patient, then they receive 403.

### US-05 Schedule an appointment

- Given an active patient and an active doctor with a free slot, when the receptionist books the doctor at 10:00 on 14 October with a reason, then the appointment is saved with status `scheduled`.
- Given a doctor who already has a `scheduled` appointment at 10:00 on 14 October, when the receptionist books the same doctor at 10:00 on 14 October, then the booking is refused and the reason is explained.
- Given a doctor whose 10:00 appointment was cancelled, when the receptionist books the same doctor at 10:00 on the same day, then the booking succeeds.
- Given a doctor with an appointment at 10:00, when the receptionist books the same doctor at 10:30 on the same day, then the booking succeeds.
- Given a deactivated patient or a deactivated doctor, when the receptionist tries to book them, then the booking is refused.
- Given a doctor, when they try to schedule an appointment, then they receive 403.

### US-06 Record diagnosis and notes

- Given a doctor and a `scheduled` appointment of their own, when they record a visit date, diagnosis and notes, then the medical record is saved and the appointment becomes `completed`.
- Given an appointment of another doctor, when a doctor tries to record its outcome, then they receive 404.
- Given a `cancelled` appointment, when the doctor tries to record its outcome, then the record is refused.
- Given an existing medical record, when anyone tries to edit or delete it, then the change is refused; corrections are made with a new entry.
- Given a receptionist or an administrator, when they try to record a diagnosis, then they receive 403.

### US-09 Deactivate a patient

- Given an active patient with past appointments and medical records, when the receptionist deactivates the patient, then the patient is marked inactive and every past appointment and record is still stored and readable by the authorized doctor.
- Given a deactivated patient, when the receptionist tries to book an appointment for them, then the booking is refused.
- Given a deactivated patient, when the receptionist searches for them, then they appear only when inactive patients are explicitly included in the search.

## Traceability from requirements to stories

Every functional requirement is covered by at least one story.

| Requirement | Stories |
|---|---|
| FR-01 | US-03 |
| FR-02, FR-03 | US-04 |
| FR-04 | US-14 |
| FR-05 | US-09 |
| FR-06 | US-02 |
| FR-07 | US-15, US-16 |
| FR-08 | US-16 |
| FR-09 | US-17 |
| FR-10 | US-15 |
| FR-11, FR-12 | US-05 |
| FR-13 | US-07, US-11 |
| FR-14, FR-15 | US-12 |
| FR-16 | US-06, US-12 |
| FR-17 | US-07, US-13 |
| FR-18 | US-06 |
| FR-19 | US-06, US-13 |
| FR-20 | US-13 |
| FR-21 to FR-25 | US-01 |
| FR-26 | US-10 |
| FR-27 | US-08 |

Non-functional requirements are not separate stories. They are applied as acceptance criteria (for example NFR-04 in every "receives 403" criterion above), as test tasks under the stories, and as pipeline checks under epic E6.
