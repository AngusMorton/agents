---
name: babysit-pr
description: Monitor a pull request through review and CI. Use when a user asks to monitor, watch, or babysit a PR.
---

# Babysit PR

All the repos we work on have various AI code review bots for code review. They're helpful, even if they're not always right.

If your harness offers tools to monitor a PR, use them so you can respond when comments arrive. Otherwise, poll the PR for new comments and checks. 

A few guidelines for handling checks and comments:
* Only act on checks and comments newer than the latest push. 
* Verify every bot finding before changing code. 
* Reproduce reported bugs before changing code.
* Theoretical findings about a state the code cannot reach should not be fixed.
* Apply [Fix the root cause](../engineering-principles/SKILL.md#fix-the-root-cause) when addressing issues.

Fix real findings and CI failures, distinguish repository failures from infrastructure flakes. Reply with a concise written reason when dismissing a false positive from an AI review bot. Never reply to feedback from a human.

If a review bot leaves feedback you believe is not worth addressing, reply and resolve the comment. Format comments left on the user's behalf like this example, with the model ID as plain text. Use your own model ID and the GitHub username of the user you're acting for:

```md
**claude-opus-5-5 responding on behalf of AngusMorton**

Fixed in 1b7302f31. `save` and `remove` now return false when the write fails.
```

Once you've acted on review feedback from a bot you will need to re-request a review from the bot that provided the feedback. If Copilot provided feedback you've acted on or pushed back on, re-request a review from only Copilot so it can provide another review.

Do not repeat this process more than 2 times. If Copilot has actionable feedback after 2 rounds, step back and reconsider the implementation from first principles.

Keep an eye on changes to `main` and rebase when needed. If an overlapping PR makes this one obsolete, stop monitoring, report it to the user, and ask before closing the PR unless closure was explicitly authorized.

Do not let review feedback expand the PR beyond the user's original goal. Address real shortcomings, but avoid scope creep.

If nothing has changed, stay quiet rather than posting filler comments. Stop when the review bots and required checks are green on the latest commit. Merge only when the user explicitly requests it; otherwise report that the PR is ready.
