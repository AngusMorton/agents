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

The reader is a reviewer who never saw your thread. The description says why the change exists, what changed, and proves the change is safe. Apply the `technical-writing` skill to it.

Open the description with a simple explanation of the problem based on the user's original prompt, so the reader understands why. Then briefly explain the solution. Do not lead with an implementation inventory:

BAD
> Remove implicit workspace carry-over from every "new thread" entry point (cmd +n / cmd+shift+o, sidebar v1/v2 buttons, command palette). New threads inherit only the project from context; branch, worktree, and env mode always come from the configured defaults. Deleted buildContextualThreadOptions, startNewThreadInProjectFromContext, and the v1 sidebar's seed-context machinery.

GOOD
> The "new worktree" default was ignored when starting new threads on existing worktrees, which was unintuitive. Now user preferences always apply.

### Describe the code, not the thread

The reviewer needs the why from the prompt, not the history of the thread. Leave these out of the description:

- Who asked, agreed, approved, or decides. State the behaviour as a fact.
- How the change differs from the brief, the spec, an audit, or a proposed direction. Describe what the code does. If you departed from what the user asked, say so in the hand-back.
- Questions and recommendations for the user. Ask them in the thread.
- Review history: which reviewer or bot found what, which commit answered it, and earlier versions of the PR. The red and green CI links, and the "Council review" section from the `council` skill, are the exceptions.
- Follow-ups and `agent-feedback` items you filed. Mention one only when the reviewer needs it to judge this PR, and then name it or link it. "Left for a follow-up" tells the reviewer nothing.

BAD
> Angus agreed to these limits:
> - Luxury-Hotels-In opens the plain hotel list, because the hotels screen has no luxury filter.

GOOD
> `/s/Luxury-Hotels-In/{place}` opens the plain hotel list. The hotels screen has no luxury filter.

BAD
> This differs from the brief in one way: the three callers that let the browser navigate still needed one line each.

GOOD
> The three anchors that let the browser navigate also pass the handler to `onAuxClick`.

BAD
> Whether to put this behind a flag is your call. I recommend shipping without a flag.

GOOD
> Logged exits step up by about 0.1% at deploy, because middle clicks start to count. Tell the data team the deploy date.

### Keep only the evidence a reviewer needs

A reviewer should finish the description in about a minute, as `technical-writing` says. Aim for under 400 words of prose. Tables, code blocks, and `<details>` blocks don't count. Scale the proof to the risk: a refactor with no visible change needs one line of proof, not a screenshot grid. Keep what shows the change is safe:

- The before and after evidence.
- The red and green CI links, when the PR has a failing-test commit.
- The numbers that size the risk, such as traffic or revenue on the affected path.
- What changes at deploy, and how to check it after release.

Cut the rest:

- Full scripts and long queries. Commit a script the reviewer will rerun, or put it in a collapsed `<details>` block.
- Every command that passed. One line names the gates.
- Long lists of what you didn't check. Name only the gap that could hide a bug in this PR.
- Restated design notes the diff already shows.

### Show transitions with video

Match the media to what the reviewer needs to see. Attach files with `gh pr create --attach` or `gh pr edit --attach`. Both take images and videos.

- For a change in how the UI moves between states, record a video of the before and the after. This covers loading, a placeholder that fills, an error and its retry, animation, focus, and timing. A series of stills can't show a transition or how long it takes.
- For a static result, take before and after screenshots of the same screen in the same state.

BAD
> Four screenshots of a sticky footer ad: pending before, pending after, closed before, closed after.

GOOD
> A video of the placeholder loading into an ad, and a video of an ad that doesn't fill, each before and after.

## Open the PR

Open a draft PR and request a review from Copilot.

If the branch has a failing-test commit followed by its fix, open the PR with only the failing-test commit. Push the fix after CI shows it red, as [Show red, then green](../engineering-principles/SKILL.md#show-red-then-green) describes. Request the reviews only after the fix is pushed, so the bots review the whole change.

After every push, rewrite the description to match the branch. Describe the final diff as if it were the first version. Don't add notes about what changed since the last push, which commits came before a rebase, or that a proof ran on an earlier commit. If a later commit changes the behaviour, prove it again on the head.

Before you hand back, read the posted description with `gh pr view --json body`. Check that every image and video is a `github.com/user-attachments` URL and not a local path such as `./err-sheet.png`, that nothing in it names the thread, the brief, or a reviewer, and that it still reads in about a minute.

If the user also asked to babysit it, continue with the `babysit-pr` skill.