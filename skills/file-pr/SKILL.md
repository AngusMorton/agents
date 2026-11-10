---
name: file-pr
description: Files a concise pull request. Use when the user asks to file, open, or create a PR.
---

# File PR

Before pushing a branch, ensure the format is prefixed with the users initials and uses a concise but descriptive name for the diff, for example:

BAD
> ❌ t3code/sentry-triage-2

GOOD
> ✅ am/fix-google-maps-issue

Do not rename the branch once it has been pushed. If the branch already exists on the remote, keep its name.

Before filing, check whether a PR for this branch already exists. Review the diff locally against `origin/main` to make sure its contents match the goal.

## PR Titles

PR titles become commit messages and must be conventional commits style. Use short imperative descriptions for the PR title. Prefer a concise, human-readable title that explains the impact or why the change matters. Commit messages should be clear, imperative, and outcome-focused:

BAD
> perf(server): negotiate permessage-deflate on the websocket

GOOD
> perf(server): cut websocket frame size by 70%+

BAD
> fix(sentry): restore sourcemap uploads, broken since the @sentry/react-router 10.70 bump

GOOD
> fix: ensure sourcemaps are uploaded
> 
> Fixes missing sourcemaps caused by configuration/breaking changes 
> introduced in the @sentry/react-router 10.70 bump.

Valid types are:
* `feat` - adding new functionality.
* `fix` - correcting a bug or unintended behaviour.
* `refactor` - restructuring code without changing behaviour.
* `chore` - maintenance, dependency updates, config changes.
* `test` - adding or updating tests only.
* `docs` - documentation only.
* `perf` - performance improvements.
* `revert` - reverting a previous commit.

Scopes are optional, use the affected package or area when helpful.

## PR Descriptions

Open the description with a simple explanation of the problem based on the user's original prompt, then briefly explain the solution. Apply the `technical-writing` skill to the PR description. Do not lead with an implementation inventory:

BAD
> Remove implicit workspace carry-over from every "new thread" entry point (cmd +n / cmd+shift+o, sidebar v1/v2 buttons, command palette). New threads inherit only the project from context; branch, worktree, and env mode always come from the configured defaults. Deleted buildContextualThreadOptions, startNewThreadInProjectFromContext, and the v1 sidebar's seed-context machinery.

GOOD
> The "new worktree" default was ignored when starting new threads on existing worktrees, which was unintuitive. Now user preferences always apply.

Include screenshots in the PR description for UI changes, before/after screenshots are very helpful for reviewers. Always use the `gh` CLI to attach files (--attach).

Open a draft PR and request a review from Copilot, then comment "@codex review" to request a review from Codex.

If the branch has a failing-test commit followed by its fix, open the PR with only the failing-test commit. Push the fix after CI shows it red, as [Sequence verifiable units](../engineering-principles/principles/principle-sequence-verifiable-units.md) describes. Request the reviews only after the fix is pushed, so the bots review the whole change.

After every push ensure that the description matches the branch.

If the user also asked to babysit it, continue with the `babysit-pr` skill.