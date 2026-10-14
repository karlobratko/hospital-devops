# Process and ceremonies

The project follows Scrum with two-week sprints. It has four development sprints, each with a sprint goal, and three ceremonies per sprint. The project has a single developer, who takes the Product Owner, Developer and Scrum Master roles in turn. Each ceremony is a deliberate change of role, recorded in the repository so that the process can be inspected afterwards.

## Roles

The Product Owner orders the [product backlog](03-product-backlog.md), decides what each sprint should achieve and accepts or rejects the result at the review. The Developer turns backlog items into a working, tested and published increment. The Scrum Master checks that the process is followed: that the Definition of Ready and Definition of Done are respected and that the retrospective produces a real change. Working alone, the Scrum Master role is mostly a check carried out at the retrospective.

## Sprint length

A sprint lasts two weeks. One-week sprints carry too much ceremony overhead for one person working alongside other courses. A month gives only two chances to inspect and adapt before the project defence, which is too coarse. Two weeks is long enough to finish a vertical slice through the whole stack and short enough to correct course four times.

The planning phase before the first sprint (Labs 01 to 03: requirements, decomposition, estimation and the GitHub Project) is not counted as a development sprint. Sprint 1 starts once the backlog is on the project board.

## Sprint outline

Each sprint has a one-sentence goal. The goals follow the order in which the course introduces the pipeline stages, so that every sprint delivers both application features and a new piece of the DevOps workflow. The stories planned into each sprint are listed in the [sprint backlogs](07-estimation-and-sprints.md), written during Lab 02 once the stories are estimated.

| Sprint | Goal | Application | Pipeline |
|---|---|---|---|
| 1 | Walking skeleton: a user logs in, and every change is built and tested automatically. | Login with roles; doctor registration | Repository conventions, branching strategy, CI building and testing on every pull request |
| 2 | Patients and doctors are managed end to end, and every merge publishes a container image. | Patient registration, search, editing and deactivation | Containerisation, image published to GitHub Container Registry |
| 3 | Receptionists schedule appointments without double booking, and doctors record their consultations. | Appointments, medical records | Continuous delivery to the prototype environment |
| 4 | The calendar is fully manageable, end-to-end tests run in the pipeline, and the application is observable. | Appointment list, updates and cancellation; medical history; CSV export and user administration if velocity allows | End-to-end tests in the pipeline, monitoring dashboard, documentation for the defence |

## Artifacts

The product backlog is kept in the [product backlog document](03-product-backlog.md) and, from Lab 03, on the GitHub Project, which is the authoritative version from then on. The sprint backlog is the set of items assigned to the current iteration on the GitHub Project, together with the sprint goal. The increment is the container image published to GitHub Container Registry at the end of the sprint, tagged with the version.

Each sprint has a record file, `docs/sprints/sprint-N.md`, created at sprint planning. It holds the sprint goal, the planned items, the work log, the review notes and the retrospective. This is what makes the process visible in the repository rather than only claimed.

## Ceremonies

### Sprint planning

Sprint planning takes place on the first day of the sprint and lasts 30 to 60 minutes, in the Product Owner role. Its input is the ordered product backlog and the velocity of the previous sprint. Only stories that meet the Definition of Ready are considered. The stories are pulled from the top of the backlog until the planned points reach the previous sprint's velocity; for sprint 1, which has no history, a capacity of 12 to 15 points is assumed. Each story is checked against its dependencies, and tasks for the selected stories are created on the project board if they do not exist yet.

The output is a sprint goal in one sentence, the sprint backlog assigned to the iteration on the GitHub Project, and a new `docs/sprints/sprint-N.md` with both written down.

### Daily work log (instead of the daily scrum)

A daily scrum synchronises a team, and with one developer there is no one to synchronise with. Claiming a daily stand-up that never happens would misrepresent the process. Instead, every working session ends with a dated entry in the sprint's work log: what was finished, what is next, and anything blocking. Each entry takes about two minutes and is the last step of the session, together with moving the cards on the board to match reality.

### Sprint review

The sprint review takes place on the last day of the sprint and lasts about 30 minutes, in the Product Owner role. The increment is run, not described: the published container image is started and each finished story is demonstrated against its acceptance criteria. Where possible the demonstration is shown to someone else, such as a classmate or the lab instructor, and their feedback is written down.

The output is the list of stories accepted as done, the sprint's velocity (the sum of points of accepted stories only), and feedback that is turned into new or re-ordered backlog items.

### Sprint retrospective

The retrospective takes place straight after the review and lasts about 20 minutes, in the Scrum Master role. It looks at how the sprint was carried out rather than at what was built: what went well, what went badly, and why. The planned and the actual velocity are compared, and so are the planned and the actual sprint content.

The output is one concrete change to try in the next sprint, written in the sprint record and checked at the next retrospective. A retrospective that ends without a change is not finished.

## Backlog refinement

Refinement is continuous rather than a fixed ceremony, but it happens for named reasons. About a tenth of each sprint, roughly two hours, is reserved for it. The goal is that the top of the backlog is always ready to be pulled into the next sprint.

Refinement is triggered by any of the following events.

A new or changed requirement. A stakeholder asks for something the backlog does not cover, such as the new feature requests expected at the project defence. It is written as a story, given acceptance criteria and estimated, and the backlog is re-ordered.

A story is too large. A story estimated at 13 points or more, or one that cannot be finished in a sprint, is split before it can be planned.

Missing acceptance criteria. A story near the top of the backlog has no Given / When / Then criteria and therefore fails the Definition of Ready.

Spillover. An item did not finish in its sprint. It is re-estimated for the remaining work and re-ordered, rather than silently moved into the next sprint.

Discovery during implementation. A technical constraint or a hidden dependency appears and changes what is realistic. The affected stories are re-estimated and their dependencies updated.

Feedback from the sprint review. The demonstration changes what the stakeholder wants next. New stories are added and the order is revised.

Every refinement that changes the backlog is recorded in the work log of the current sprint, with the reason, so that changes in order can be traced back to the event that caused them.
