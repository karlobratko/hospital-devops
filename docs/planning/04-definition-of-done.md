# Definition of Ready and Definition of Done

Acceptance criteria say when one particular story is finished. The Definition of Done is a single list that applies to every story and task in the project. The Definition of Ready says when a story is clear enough to be pulled into a sprint. All three are checked, not assumed.

## Definition of Ready

A user story may enter sprint planning only when all of the following are true.

- It follows the template and names one of the three roles.
- It has acceptance criteria in Given / When / Then form, including at least one criterion for a refused or invalid case.
- It is estimated in story points and is smaller than 13 points.
- Its dependencies are known and recorded, and none of its blocking stories is planned for a later sprint.
- It can be finished within one sprint.

A story that fails any of these goes back to backlog refinement.

## Definition of Done

A user story or task is done only when every item below is true.

- All acceptance criteria are met.
- The change is merged into `master` through a pull request that is linked to its issue. Nothing is pushed directly to `master`.
- The pull request was reviewed before merging. Working alone, this is a self-review against this list, recorded as a comment on the pull request.
- Unit and integration tests for the change are written and pass in CI.
- The end-to-end test for the affected user flow passes in CI.
- Every refused case in the acceptance criteria that comes from the [RBAC matrix](02-roles-and-access.md) has an automated authorization test.
- The manual test case for the story, if it has one, has been executed and its result recorded in the issue.
- The container image builds in CI and is published to GitHub Container Registry.
- Documentation is updated where a requirement, decision or interface changed.
- The issue is closed and its card is in the Done column of the project board.

Items that depend on pipeline stages not yet built (CI, end-to-end tests, image publishing) apply from the sprint in which that stage is introduced, as planned in the [process](05-process.md). Until then the remaining items still apply.
