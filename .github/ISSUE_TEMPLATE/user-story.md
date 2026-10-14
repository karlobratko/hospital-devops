---
name: User story
about: A user-facing piece of functionality
title: "US-<nn> <short-name>"
labels: user-story
---

## Story
As a <role>, I want <goal>, so that <benefit>.

## Requirements
<FR-xx, NFR-xx>

## Acceptance criteria
- [ ] Given ... when ... then ...
- [ ] Given ... when ... then ... (at least one refused or invalid case)

## Definition of Done
See docs/planning/04-definition-of-done.md.
- [ ] All acceptance criteria are met
- [ ] Merged into `master` through a pull request linked to this issue
- [ ] Pull request self-reviewed against this checklist, recorded as a comment
- [ ] Unit and integration tests written and passing in CI
- [ ] End-to-end test for the affected flow passing in CI
- [ ] Every refused RBAC case has an automated authorization test
- [ ] Manual test case (if any) executed and its result recorded below
- [ ] Container image builds in CI and is published to GHCR
- [ ] Documentation updated where a requirement, decision or interface changed

## Dependencies
Blocked by: #<issue> (also set under Relationships → Mark as blocked by)

## Story points
<1, 2, 3, 5 or 8, from docs/planning/07-estimation-and-sprints.md>

## Manual test result
<Filled in before closing, if the story has a manual test task.>
