# Contributing

Every change follows the same path, so that each one can be traced from a requirement to a published image:

```text
requirement → issue → project board → branch → commits → pull request → CI → merge to master → image in GHCR
```

The rules below apply to every change from Lab 03 onward. The initial planning documents were committed directly to `master` before these rules existed; no further direct commits are made.

## Issues

All work starts from an issue, created from one of the templates in `.github/ISSUE_TEMPLATE/`:

| Template | Label | Used for |
|---|---|---|
| Epic | `epic` | A theme grouping stories, e.g. `E4 – Appointment scheduling` |
| User story | `user-story` | A piece of user-facing functionality, e.g. `US-05 schedule-appointment` |
| Enabler | `user-story`, `enabler` | Platform and pipeline work at story level, e.g. `EN-02 ci-pipeline` |
| Task | `task` | One implementable change, e.g. `T-05.3 Reject clashing appointment for the same doctor` |
| Bug | `bug` | A defect found in merged code |

Every issue title starts with its identifier from the [task decomposition](docs/planning/06-task-decomposition.md). Test tasks carry `[TEST][auto]` or `[TEST][manual]` after the identifier. Stories are sub-issues of their epic, and tasks are sub-issues of their story. Dependencies are set with **Relationships → Mark as blocked by**, not only written in the description.

## Branches

The default branch is `master`. It is protected: changes reach it only through pull requests whose CI checks pass.

Branch names follow this pattern:

```text
<issue-number>-<item-id>-<short-description>
```

```text
23-t-05-3-conflict-rule
31-us-05-schedule-appointment
42-t-en2-2-ci-workflow
57-bug-login-redirect-loop
```

The issue number comes first so that automation can parse it. The rest is lower case, with words separated by hyphens. There is one branch per task, or per small story without tasks, and each branch is created from an up-to-date `master`.

A branch is always created from its issue, so that GitHub links them. Either use **Development → Create a branch** in the issue sidebar, or run:

```bash
gh issue develop 23 --name 23-t-05-3-conflict-rule --base master --checkout
```

A branch created with `git checkout -b` and pushed by hand is not linked to its issue. If that happens, delete it and create it again from the issue.

A branch created on GitHub is fetched and checked out locally with:

```bash
git fetch origin
git switch 23-t-05-3-conflict-rule
```

Merged branches are deleted. This is automatic once **Settings → General → Automatically delete head branches** is enabled.

## Commits

Commits follow [Conventional Commits](https://www.conventionalcommits.org/):

```text
<type>(<scope>): <summary in the imperative, lower case, no full stop>

<optional body: what and why, wrapped at 72 characters>

Refs: #<issue-number>
```

Types:

| Type | Used for |
|---|---|
| `feat` | New behaviour visible to a user |
| `fix` | A bug fix |
| `test` | Adding or changing tests only |
| `refactor` | A code change that neither fixes a bug nor adds behaviour |
| `docs` | Documentation only |
| `build` | Gradle, Dockerfile, Compose, dependencies |
| `ci` | GitHub Actions workflows |
| `chore` | Repository plumbing that fits nothing above |

Scopes are the areas of the application: `auth`, `patients`, `doctors`, `appointments`, `records`, `export`, `users`, and for non-code work `planning`, `diagrams`, `readme`. A breaking change adds `!` after the scope and a `BREAKING CHANGE:` footer.

The issue is referenced in a `Refs: #<number>` footer rather than in the subject line. Squash merging appends the pull request number to the subject, and two numbers in one subject line are easy to confuse. The footer still makes the commit appear on the issue's timeline.

```text
feat(appointments): reject a second booking for the same doctor and slot

The check runs in the booking service, and a unique partial index on
(doctor_id, slot_start) where status = 'scheduled' guards against two
concurrent inserts.

Refs: #23
```

## Pull requests

A pull request targets `master` and is opened as soon as the branch has its first meaningful commit, as a draft if the work is unfinished.

The pull request title is a Conventional Commit subject, because squash merging uses it as the commit on `master`. The description, prefilled from `.github/pull_request_template.md`, contains `Closes #<issue-number>`, which closes the issue on merge and moves its card to Done. It also contains the Definition of Done checklist from [docs/planning/04-definition-of-done.md](docs/planning/04-definition-of-done.md).

A pull request is merged only when all CI checks pass and every applicable Definition of Done item is ticked. With a single developer, the review is a self-review against the checklist, recorded as a comment on the pull request before merging.

Pull requests are merged with **Squash and merge**, so that `master` has one commit per task and its history reads as a list of finished work.

## Project board

The board reflects reality. When a branch is created, its card moves to In Progress. When a pull request is opened for review, the card moves to In Review. Closing and merging move cards to Done automatically. The work-in-progress limits on In Progress and In Review are respected.
