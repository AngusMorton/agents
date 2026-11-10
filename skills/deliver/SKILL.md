---
name: deliver
description: Turns a task into a PR that does what was asked, with proof, and follows our engineering principles. Use when the user asks for a code change that should end in a PR, or says "deliver".
---

# Deliver

Run every step in order. If you skip a step, say which one and why when you hand back.

When you brief another thread, write "Use `deliver`" instead of listing these steps.

## 1. Write down the goal

Before you read code, restate the user's task as one to three outcomes a reviewer can check. For example: "Play Store links carry `web_aqid` in the referrer." 

Write one outcome for each consumer of the change, and be specific about where that consumer sees the result. For example: "The data engineering team can join an install to its web session in the `events` table in BigQuery."

Step 5 proves each outcome, and step 7 judges bot findings against them.

## 2. Load the skills that fit

Load `engineering-principles` and read the principle files for every section that matches the task. Then load the skills that fit:

- For a bug, use `fix-issue`. Don't use it for features or config changes.
- For a change to shared code, dependencies, or a data contract, use `blast-radius`.
- For a subsystem you don't know, use `how`.

## 3. Make the change

Make the smallest change that meets the goal, with focused tests, then run the `simplify` skill on your diff to identify opportunities for simplification and code clean up.

Run the repo's format, lint, type-check, test, and build commands until they pass.

## 4. Review Loop

1. Check the diff against the "Reviewing code" section of `engineering-principles`
2. Request a code review by delegating to Codex 6.1 Sol through T3 Code, or `/code-review` if 6.1 Sol is unavailable.

Check each finding against the code before you fix it. Don't fix issues raised unless you can prove/reproduce the issue.

Repeat this step at most 3 times until the code review agent approves the change. 

## 5. Prove it works

Prove each outcome from step 1 on the final code, in the running artifact. Passing CI, a clean type check, and your reading of the diff are not proof. See [Prove it works](../engineering-principles/principles/principle-prove-it-works.md).

Match the evidence to the change:

- UI: before and after screenshots of the same screen in the same state.
- Bug: the repro output before and after the fix.
- Performance: before and after measurements, with the run count and spread.
- Analytics or data: the real event payload or query result.

If you can't reach an environment where the change runs, ask the user for one. If an outcome stays unproven, say so in the PR and the hand-back. Don't describe it as tested.

## 6. File the PR

Use `file-pr`. Put the step 5 evidence in the description.

## 7. Babysit the PR

Use `babysit-pr`. Before you act on a bot finding, check that this PR introduced the problem and that fixing it serves the goal from step 1. If not, dismiss it with a reason or ask the user. If a fix changes behaviour, prove that outcome again.

## 8. Hand back

Don't merge. Reply with:

- The PR link.
- How you proved each outcome, and any outcome you couldn't prove.
- What `/simplify` and the Review Loop changed, and the findings you dismissed and why.
