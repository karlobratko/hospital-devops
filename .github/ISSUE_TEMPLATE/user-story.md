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
- [ ] Pull request self-reviewed, recorded as a comment
- [ ] Unit and integration tests written and passing in CI
- [ ] End-to-end test for the affected flow passing in CI
- [ ] Every refused RBAC case has an automated authorization test
- [ ] Manual test case (if any) executed, result recorded in a comment
- [ ] Container image builds in CI and is published to GHCR
- [ ] Documentation updated where a requirement, decision or interface changed
