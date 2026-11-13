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

PR titles become commit messages and must be conventional commits style. A title is a headline, not a sentence: someone scanning the PR list or `git log` should know what changed without opening it. Say the outcome in imperative mood. The why goes in the description.

* Keep the text after `type(scope): ` under 50 characters. A ticket suffix doesn't count.
* Drop articles and qualifying clauses. The `technical-writing` rules apply to the description, not the title.
* Use words the reader already knows: what a user sees, or a real code symbol. Not names you coined while working, such as "the visit" or "wait screen".
* Don't repeat the scope, repo, or platform in the text.
* If the work has a Jira ticket, end with its key: `(MIS-4)`.
* When one change ships in several repos, give each PR the same title.
* The same rules apply to commit subjects, because `gh stack submit --auto` and squash merges reuse them.

BAD
> fix(accounts): keep accounts out of release builds whatever the backend flag says

GOOD
> fix(accounts): always hide accounts in release builds

BAD
> feat: add the visit to every link into the Android app

GOOD
> feat: add web attribution to all Android app links

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

If users see no change, use `refactor` or `chore`, even when the work enables a later feature.

Scopes are optional, use the affected package or area when helpful.

## PR Descriptions

Open the description with a simple explanation of the problem based on the user's original prompt, then briefly explain the solution. Apply the `technical-writing` skill to the PR description. Do not lead with an implementation inventory:

BAD
> Remove implicit workspace carry-over from every "new thread" entry point (cmd +n / cmd+shift+o, sidebar v1/v2 buttons, command palette). New threads inherit only the project from context; branch, worktree, and env mode always come from the configured defaults. Deleted buildContextualThreadOptions, startNewThreadInProjectFromContext, and the v1 sidebar's seed-context machinery.

GOOD
> The "new worktree" default was ignored when starting new threads on existing worktrees, which was unintuitive. Now user preferences always apply.

Include screenshots in the PR description for UI changes, before/after screenshots are very helpful for reviewers. Always use the `gh` CLI to attach files (--attach).

Open a draft PR and request a review from Copilot.

If the branch has a failing-test commit followed by its fix, open the PR with only the failing-test commit. Push the fix after CI shows it red, as [Show red, then green](../engineering-principles/SKILL.md#show-red-then-green) describes. Request the reviews only after the fix is pushed, so the bots review the whole change.

After every push ensure that the description matches the branch.

If the user also asked to babysit it, continue with the `babysit-pr` skill.