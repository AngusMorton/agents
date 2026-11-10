---
name: engineering-principles
description: "Rome2rio's rules for a code change that ends in a pull request: prove it works, commit the failing test before the fix, keep the diff small, and when each rule gives way to another. Use when you write, fix, refactor, migrate, test, or review production code. The `deliver` and `fix-issue` skills load it. Not for prototypes, one-off scripts, or questions about code. Incidents and team playbooks belong to `rome2rio-engineering`."
---

# Engineering principles

These are the rules for changing production code at Rome2rio. Every rule you must follow is on this page. The two files under `references/` are catalogues, and each link says when to open its file.

Read "Every change" for every task. Then read each later section that matches the task. Most tasks match more than one.

Some rules pull against each other. Where they do, the rule gives a default and the condition under which the default gives way.

## Every change

### Prove it works

Before you call a change done, check the result in the place where each consumer of the change uses it. A consumer is a person or a system that uses the result. A traveller looks at a screen. The data team queries a table in BigQuery. Another service reads an API response. A green build, a clean type check, and your reading of the diff are not proof.

- Run the real thing: the app on a device or emulator, the endpoint in an ephemeral environment, the dbt model on the dev target. Read the actual value, not a cached or derived copy.
- Match the evidence to the change. For UI, take before and after screenshots of the same screen in the same state. For a bug, capture the repro output before and after the fix. For data, show the real event payload or query result.
- For a number, give the run count and the spread, and measure both sides the same way. Rule out what else explains the number: errors, a debug build compared with a release build, a warm start compared with a cold start, a BigQuery result served from cache.
- Prefer a check that a reviewer can rerun, such as a test, a script, or a saved query. Paste the output.
- You can read production data. Never write to production and never deploy to it. Query the smallest date range that answers the question, and check how many bytes a BigQuery query scans before you run it.
- When a check fails and you don't expect it to, confirm that the check ran against your change before you edit code. A stale build on the device is a common cause.
- If you can't reach the place where a consumer uses the result, ask the user for access. If an outcome stays unproven, say so. Don't describe it as tested.

### Show red, then green

When a fix has a test, commit the failing test first and the fix on top. When you open a PR, open it with only the test commit. After CI shows the test failing for the reported reason, push the fix. The reviewer sees the failure and then the pass, and doesn't have to take your word that the test covers the bug.

Skip the test commit when the code has no cheap place for a test. The before and after repro output is then the proof.

The same order works beyond bug fixes. Commit a refactor that changes no behavior before the feature that needs it. Capture a baseline measurement before the optimization.

**Default.** Every commit builds and passes its checks, so a reviewer can read the PR commit by commit.

**Gives way when** a commit fails on purpose. There are two cases. The first is the failing test that comes before its fix. The second is a migration step inside one PR, where you delete the old function first and use the compiler errors as the list of callers left to migrate. In both cases the last commit is green. A PR never merges red, and that includes each PR in a stack.

### Make the smallest change

Make the smallest change that meets the goal.

- Look for code to delete before you add code.
- Don't build for a case that doesn't exist yet. Leave out the option, flag, wrapper, validator, or abstraction that nothing in this change uses. This rule is YAGNI, short for "you aren't gonna need it".
- Leave code the task didn't ask about as it is. If you notice a problem there, report it in your reply.

**Default.** The smallest diff.

**Gives way when** the small change would do one of these:

- Add another branch to an `if`, `when`, or `switch` chain that already branches on the same question.
- Add a second flag or nullable field that must stay in sync with one that is already there.
- Repeat the same special case in three or more places.
- Contradict something the current design assumes, for example that a search has one origin and one destination.

Then redesign. Read every file the requirement affects, and ask what you would build if the requirement had been there from the start. Commit the redesign first with no change in behavior, then put the feature on top.

A bug fix has a stricter rule. Change only what removes the root cause, even when that means a tighter type. Propose everything else in your reply, such as a wider redesign or a new lint rule.

## Fixing a bug

The `fix-issue` skill has the step-by-step workflow. These rules apply with or without it.

### Fix the root cause

Reproduce the bug before you change code. Then ask why the bad state exists until you reach the code that created it, and fix it there. The line that throws is often not the line that is wrong.

- Don't add a null check, a `try`/`catch`, or an early return to silence the error. Add one only when the evidence shows that the missing value is a legitimate state.
- When you are stuck, add logging and read the real value. Don't guess.
- When a bug appears only after an app update, a relaunch, or a redeploy, suspect stored state before code. Look at the DataStore or `UserDefaults` value, the database row that an older version wrote, and the cached response.
- When two fixes have failed the same check, stop. Write down the assumption that both fixes share, and test that assumption before you write a third fix.
- Search the codebase for the same pattern. Fix the instances that share this root cause and list the others in your reply.
- If the same mistake can come back, name the check that would stop it: a type, a detekt or lint rule, or a test. Propose the check in your reply.

## Designing types and data

### Model the domain in types

Make the compiler reject a state that can't happen, so that no code has to check for it at runtime.

- Parse at the boundary. Turn an API response, a deep link, a config value, or a stored row into a domain type once, at the point where it enters the code. Past that point, trust the type and don't validate again.
- Model alternatives as one closed type, like `sealed interface LoadState` with `Idle`, `Loading`, `Success`, and `Failed`. Don't use a set of booleans and nullable fields that lets contradictory combinations compile.
- Give an ID or a code its own type, like `value class UserId`, so that a caller can't pass one `String` where another belongs.
- Match exhaustively. In Kotlin and Swift, write no `else` on a `when` over a sealed type and no `default` on a `switch` over an enum. A new case then fails the build at each place that must handle it.
- Don't cast past the compiler with `!!`, `as!`, `try!`, or `as any`. Trace the value back to the boundary and parse it there.
- Keep business logic in functions that take values and return values, so a test can call them without the framework.

**Default.** Use the types that are already there.

**Gives way when** your change would add a field that must agree with another field, or when a type that admits an impossible state is the root cause of the bug you are fixing. Tighten the type then. Don't retype working code that the task doesn't touch.

When you design a new type or review a function signature, read [Type patterns](references/type-patterns.md). It gives the Kotlin, Swift, and TypeScript form of each rule and the patterns that don't fit on this page.

## Writing or changing tests

### Test behavior, not implementation

A test calls the code the way its callers do and asserts a literal expected value on the result that the callers observe.

- Ask of every test whether it would still pass if the code under test did nothing. If it would, rewrite the assertion or delete the test.
- Assert the result or the state after the call, not which collaborators the code called.
- Write a few focused tests. Don't add smoke tests, and don't add a test that only proves deleted code is gone.
- Prefer no test to a test that mostly exercises fakes, depends on timing, or needs heavy setup for a small change.
- Don't edit or weaken an existing test to make a wrong implementation pass.

When you write a test, or review a test whose assertion you doubt, read [Test shapes that cannot fail](references/test-shapes.md). It lists the five shapes with the Kotlin, Swift, TypeScript, and C# assertions to look for, and the fix for each.

## Replacing an API or running a migration

### Finish the migration

When you replace an API, migrate every caller and delete the old API in the same PR. Don't keep both paths, and don't write compatibility code that exists only to keep a half-migrated state working.

**Default.** Migrate and delete together.

**Gives way when** something you can't change in this PR still uses the old path:

- A backend endpoint or response field that shipped app versions call. Old iOS and Android builds keep calling it until travellers update.
- An analytics event, table, or column that a dbt model, a Looker dashboard, or another team reads.
- A URL or deep link that is already public.
- A Terraform module variable that another repo sets.

Then add the new path beside the old path and leave the old path working. State in the PR what must be true before the old path can go, such as traffic to the old endpoint reaching zero. Use the `blast-radius` skill to find callers outside the diff.

For a change that repeats across many files, check each unit before you start the next one. A break then shows up at the unit that caused it.

**Default.** Make the repeated edits by hand. A script is one more thing to write and review.

**Gives way when** the same mechanical edit applies to about ten or more places, or when the script is itself the proof. Do the first place by hand, script the rest, and include the script so a reviewer can rerun it. Write the smallest script that does the job.

## Writing code that retries, reruns, or restarts

### Make operations idempotent

For any operation that can run twice, ask two questions. What happens if it runs twice? What happens if the last run stopped halfway? Both must end in the same state as one clean run.

- A dbt incremental model with `insert_overwrite` replaces whole partitions, so a rerun or a backfill gives the same table. A model that appends rows counts them twice on a rerun.
- A mobile client on a bad connection can send the same write twice. Make the second request change nothing.
- A Kubernetes Job or CronJob can restart partway through. Have it read what already exists and continue from there, or write with an upsert.

If the answer to either question is "it depends on what the last run left behind", add a step that reads the current state and reconciles it.

## Refactoring

### Cut layers and state

A reader must be able to answer "where does this value come from?" and "what can change it?" without opening more than a few files. Two things make that hard: layers to trace and state to hold.

- Inline a wrapper that has one caller, and a layer that only passes the same arguments through.
- Decide each thing in one place. When two files make the same choice, put the choice behind one function and pass the result.
- Derive a value instead of storing a copy that must stay in sync. Prefer a local to a field, and a field to a global.
- Before you add a layer or a piece of state, check that it removes at least as much tracing somewhere else.

Refactor the code that your change touches. Refactor other code only when the user asks.

## Reviewing code

Use the `blast-radius` skill to find what the change breaks outside the diff. Within the diff, ask these questions:

1. Does the diff contain code that the goal doesn't need? See "Make the smallest change".
2. Does it add a wrapper, a pass-through layer, or a second copy of state? See "Cut layers and state".
3. Does it add a cast, a match that isn't exhaustive, or fields that can contradict each other? See "Model the domain in types".
4. Would each new test still pass if the code under test did nothing? See "Test behavior, not implementation".
5. Does it delete or change anything that a shipped app, another repo, or a dashboard still reads? See "Finish the migration".

## Changing these principles

Don't edit this skill or any other skill file unless the user asks you to. Propose the change in your reply.

## Attribution

Adapted from [pstack](https://github.com/cursor/plugins/tree/main/pstack) by Lauren Tan, under the MIT license. The license text is in [LICENSE-pstack.txt](LICENSE-pstack.txt).
