---
name: triage-sentry-issues
description: Triages Sentry issues. Use when the user asks to triage Sentry issues.
---

We use Sentry for error tracking in our applications. 

You'll need to identify the Sentry projects linked to this repo, then use the Sentry CLI to look for issues that indicate a problem with the code in the repo or its configuration.

Prioritise errors in our app that are still present in the code. Some errors may have already been fixed, so before you present them to the user ensure that the root cause is still in the codebase. If there are no obvious errors in our app code, look at the noisiest errors and investigate them. The goal is to reduce the amount of issues in Sentry and improve our application health overall.

Do not fix anything unless explicitly asked. 