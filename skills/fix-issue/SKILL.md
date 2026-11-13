---
name: fix-issue
description: Fixes a bug with a reproduced root cause and before-and-after proof. Use when the user asks to fix a bug, a Sentry issue, or a GitHub issue.
---

# Fix issue

The goal is a fix that is proven correct, not one that looks plausible. Every line you ship traces back to evidence about this bug. A change that "might help" is a hypothesis, and hypotheses don't ship.

Work through the steps in order. If you skip a step, say which one and why in your reply.

## 1. Check that it still needs fixing

Confirm the faulty code is still on `origin/main`. Search open PRs and recent commits for an existing fix. If you find one, don't write a competing patch. Verify it instead. Show the bug on the base revision and its absence with the fix applied, then report back.

## 2. Reproduce it

Before you run anything, write down the correct behavior and the broken behavior in one sentence each. Then make the broken behavior happen.

When the code path has a cheap test target, write a failing test first. Put it in the narrowest test layer that already covers that code, and don't build new test setup just to have a test. The test checks the intended behavior, not the current implementation. If the bug is flaky, make the test deterministic. Write it per [Test behavior, not implementation](../engineering-principles/SKILL.md#test-behavior-not-implementation).

Prefer no test over a bad one. A bad test mostly exercises mocks, depends on timing, or needs heavy setup for a small fix. Without a test, use the closest thing that runs the real code: a script, a `curl`, a CLI invocation, or the running app.

Don't edit or weaken existing tests to match a wrong implementation.

For a Sentry issue pull the latest event and a few older ones. Read the stack trace, breadcrumbs, tags, and request data. Rebuild the failing input from the event payload and feed it to the real code. The first-seen release tells you which commit range to read for a regression.

The repro must fail for the reported reason. A matching exception type is not enough. Match the message and the frame.

If it won't reproduce directly, synthesize the trigger, tighten the conditions, or add instrumentation until it fires. If it still won't, stop. Report what you tried and your best hypothesis, and let the user decide whether to continue. Don't fix a bug you haven't seen.

## 3. Find the root cause

Apply [Fix the root cause](../engineering-principles/SKILL.md#fix-the-root-cause) throughout this step. Before you list candidate causes, run the `how` skill on the subsystem the bug is in, so the candidates come from how the code works and not from a guess. If you hand the investigation or the fix to a subagent, tell it to run `how` first too. Then list the candidate causes, and rule them out with evidence until one survives. Each pass, test the split that rules out the most remaining candidates. Get evidence by running code, adding temporary logging, or reading the real value. Don't guess.

The line that throws is often not the line that's wrong. Keep asking why the bad state exists until you reach the code that created it. When the bug is a regression, or before you change defensive or odd-looking code, use the `why` skill on it. Scope it to source control and the error tracker unless the user asks for a wider search. The code may exist for a reason it doesn't show.

## 4. Make the smallest fix the evidence justifies

Fix the bug where it starts, not where it surfaces. Keep the diff small, as [Make the smallest change](../engineering-principles/SKILL.md#make-the-smallest-change) describes. Don't add a null check, `try`/`catch`, or early return just to silence the error. Add one only when the evidence shows the missing value is a legitimate state.

Revert anything a disproven hypothesis motivated, including debug logging. Search the codebase for the same pattern. Fix the instances that share this root cause, and list the others in your reply.

If two fixes fail the same check, write down the assumption they share and test it before a third.

## 5. Prove it

Check the real thing, not a proxy, as [Prove it works](../engineering-principles/SKILL.md#prove-it-works) describes.

1. Rerun the repro from step 2. It must now pass, on the same surface where it failed. An inconclusive result is not a pass.
2. Run the tests, type checks, and lint for the code you touched.
3. Check what else the change could break with the `blast-radius` skill on your diff.
4. Get an independent review. Give a fresh subagent the issue, the diff, and the repro, but not your reasoning. Ask it whether the diff fixes the root cause and what it could break. Verify each finding against the code before you act on it.

## 6. Hand back

If you wrote a failing test, commit it before the fix so the history shows it failing first. Open a PR only when the user asks, and then use the `file-pr` skill. Before you push the fix, follow [Show red, then green](../engineering-principles/SKILL.md#show-red-then-green) to show the test failing on CI.

Reply with:

- What was broken, from the user's point of view.
- The root cause, with `file:line`.
- The fix.
- The repro output before and after the fix, pasted verbatim.
- If you pushed, the red and green CI run URLs.
- A verdict of fixed, not fixed, or inconclusive.
- Anything you couldn't verify, labeled as unverified.
