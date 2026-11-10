---
name: principle-prove-it-works
description: "Apply after completing a task, before declaring done. Verify against the real artifact (run the feature, read the actual value, inspect the diff), not a proxy, self-report, or 'it compiles.'"
user-invocable: false
---

# Prove It Works

Verify every task output by checking the real thing directly. Do not infer from proxies, self-reports, or "it compiles."

**Why:** Unverified work has unknown correctness. Indirect verification (file mtimes, output freshness, agent self-reports, cached screenshots) feels cheaper than direct observation. Acting on a wrong inference costs far more than checking the source.

Check the real thing, not a proxy:
- Check process liveness directly, not indirectly through derived state
- Read the actual value, not a cached or derived representation
- When verification fails, suspect the observation method before suspecting the system

## Prove it where each consumer uses it

The work is done when each consumer of the change gets the right result. Proof that your code sends the result is not enough. A consumer is any person or system that uses the result. For example, travellers use the app, and the data engineering team queries events in BigQuery. Check the result where each consumer uses it: the screen, the API response, or the table row.

- Use an ephemeral environment, a test build, or test data.
- You can read production data. Never write to production or deploy to it.
- Query the smallest date range that answers the question. Before you run a production query, check how many bytes it scans.
- If you can't reach a consumer's system, ask the user for access. If you still can't reach it, list that outcome as unproven.

## Script the check when you can

The strongest proof is a deterministic script that re-runs the same comparison, not a one-time eyeball. Write the script, run it, and keep its output as an artifact a reviewer can re-run instead of trusting your word.

Keep the artifact visible for the human. Commit it only for large or complex work where the trail has to be auditable later, like a big port or migration.
