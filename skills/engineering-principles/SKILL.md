---
name: engineering-principles
description: "Entry point for how we do engineering. Use at the start of any coding task when writing  or reading code."
---

# Engineering principles

Find the sections that match your task and read every principle file they list before you start. Most tasks match more than one section. A feature that adds a new type matches both "Building a feature" and "Designing types". Read all that match.

## Every task

- [Prove it works](principles/principle-prove-it-works.md). Before you call the work done, check the real artifact: run the feature, read the actual value. "It compiles" and an agent's self-report are not proof.
- [Build the lever](principles/principle-build-the-lever.md). Unless the change is a couple of obvious edits, write the script, codemod, or check that does it, so a reviewer can rerun it.
- [Laziness protocol](principles/principle-laziness-protocol.md). Make the smallest change that solves the problem. Look for deletions before additions.
- [Encode lessons in structure](principles/principle-encode-lessons-in-structure.md). When the user corrects you, or you write the same instruction a second time, turn the lesson into a type, lint rule, or check.

## Fixing a bug

Use the `fix-issue` skill for the step-by-step workflow.

- [Fix root causes](principles/principle-fix-root-causes.md). Reproduce the bug, then ask why until you reach the cause, and fix it there. Don't add a guard that silences the symptom. Grep for the same pattern and fix every instance. If it fails after a restart, suspect stale persistent state before code.
- [Test behavior, not implementation](principles/principle-test-behavior-not-implementation.md). Write the failing test first, against behavior a user of the code observes.
- [Sequence verifiable units](principles/principle-sequence-verifiable-units.md). Commit the failing test, then the fix on top, so the history shows red then green.
- [Attack the premise](principles/principle-attack-the-premise.md). When two fixes that share one premise have failed the same check, stop. Write the premise down and test it directly before you write another fix that assumes it.

## Building a feature or adding a requirement

- [Redesign from first principles](principles/principle-redesign-from-first-principles.md). Design as if the requirement had been there from day one. Don't bolt it on.
- [Subtract before you add](principles/principle-subtract-before-you-add.md). Remove dead code and stale references first, then build on the simpler base.
- [Model the domain](principles/principle-model-the-domain.md). Encode the domain in a structure such as a state machine, union, or lookup table. A new feature that adds one more branch to an if/else chain skipped this step.
- [Experience first](principles/principle-experience-first.md). When user experience and implementation convenience conflict, choose the experience. The user includes the colleague who imports or maintains the code.
- [Exhaust the design space](principles/principle-exhaust-the-design-space.md). If the codebase has no precedent and the answer isn't obvious, build two or three competing prototypes and compare them before you commit.

## Designing types, data, and boundaries

- [Type system discipline](principles/principle-type-system-discipline.md). Make illegal states unrepresentable, brand semantic primitives, and match exhaustively. Don't cast your way past the compiler.
- [Boundary discipline](principles/principle-boundary-discipline.md). Validate and parse at system boundaries such as CLI args, config, and external APIs. Trust typed data inside. Keep business logic in pure functions.
- [Model the domain](principles/principle-model-the-domain.md). Pick the data structure that makes invalid states impossible instead of checking for them.

## Refactoring or simplifying

- [Laziness protocol](principles/principle-laziness-protocol.md). Prefer deletion, keep the call hierarchy flat, and put each decision behind one source of truth.
- [Minimize reader load](principles/principle-minimize-reader-load.md). Count the layers a reader must trace and the state they must hold. Collapse one-caller wrappers and pass-through layers. Shrink mutable scope.
- [Subtract before you add](principles/principle-subtract-before-you-add.md). Cut first, then reshape what's left.
- [Migrate callers, then delete legacy APIs](principles/principle-migrate-callers-then-delete-legacy-apis.md). When you replace an internal API, migrate every caller and delete the old one in the same change.

## Running a migration, rewrite, or sweep

- [Outcome-oriented execution](principles/principle-outcome-oriented-execution.md). Converge on the target architecture. Planned, scoped breakage along the way is fine. Throwaway compatibility code is not.
- [Migrate callers, then delete legacy APIs](principles/principle-migrate-callers-then-delete-legacy-apis.md). Don't leave the old path alive for internal callers.
- [Sequence verifiable units](principles/principle-sequence-verifiable-units.md). Break the work into small units and check each one before you start the next.
- [Build the lever](principles/principle-build-the-lever.md). Do the first unit by hand to learn the recipe, then script the rest.

## Writing or changing tests

- [Test behavior, not implementation](principles/principle-test-behavior-not-implementation.md). Call the code the way its users do and assert a literal expected value. If the test still passes when every import returns `undefined`, rewrite it or delete it.

## Writing code that runs concurrently, restarts, or retries

- [Make operations idempotent](principles/principle-make-operations-idempotent.md). Ask what happens if it runs twice, or if the last run crashed halfway. Either way it must converge to the same end state.
- [Separate before serializing shared state](principles/principle-separate-before-serializing-shared-state.md). Give each actor its own file, key, or branch. Add a lock only when one shared writer is a real invariant.

## Measuring performance or reporting a number

- [Explain the number](principles/principle-explain-the-number.md). Before you trust a speedup, regression, latency, or eval result, name what limits it and rule out errors, cached work, and noise. Report the run count and spread with it.

## Reviewing code

Use the `blast-radius` skill to find what the change breaks outside the diff. Within the diff, check it against these:

- [Minimize reader load](principles/principle-minimize-reader-load.md)
- [Laziness protocol](principles/principle-laziness-protocol.md)
- [Type system discipline](principles/principle-type-system-discipline.md)
- [Test behavior, not implementation](principles/principle-test-behavior-not-implementation.md)
