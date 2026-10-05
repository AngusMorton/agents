---
name: file-pr
description: Files a concise pull request. Use when the user asks to file, open, or create a PR.
---

# File PR

Before filing, check whether a PR for this branch already exists. Review the diff locally against `origin/main` to make sure its contents match the goal.

## PR Titles

PR titles become commit messages and must be conventional commits style. Use short imperative descriptions for the PR title. Prefer a concise, human-readable title that explains why the change matters:

BAD
> ❌ perf(server): negotiate permessage-deflate on the websocket

GOOD
> ✅ perf(server): cut websocket frame size by 70%+

BAD
> ❌ fix(sentry): restore sourcemap uploads, broken since the @sentry/react-router 10.70 bump

GOOD
> ✅ fix(sentry): restore sourcemap uploads

The type must be one of:
* `feat` - adding new functionality.
* `fix` - correcting a bug or unintended behaviour.
* `refactor` - restructuring code without changing behaviour.
* `chore` - maintenance, dependency updates, config changes.
* `test` - adding or updating tests only.
* `docs` - documentation only.
* `perf` - performance improvements.
* `revert` - reverting a previous commit.

## PR Descriptions

Open the description with a simple explanation of the problem based on the user's original prompt, then briefly explain the solution. Do not lead with an implementation inventory:

BAD
> ❌ Remove implicit workspace carry-over from every "new thread" entry point (cmd +n / cmd+shift+o, sidebar v1/v2 buttons, command palette). New threads inherit only the project from context; branch, worktree, and env mode always come from the configured defaults. Deleted buildContextualThreadOptions, startNewThreadInProjectFromContext, and the v1 sidebar's seed-context machinery.

GOOD
> ✅ The "new worktree" default was ignored when starting new threads on existing worktrees, which was unintuitive. Now user preferences always apply.

Open a draft PR and request a review from Copilot, then comment "@codex review" to request a review from Codex.

If the user also asked to babysit it, continue with the `babysit-pr` skill.