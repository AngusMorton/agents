* `principle-boundary-discipline`. 
* `principle-build-the-lever`. Any non-trivial work. Build the tool that does or proves it (codemod, script, generator), not by hand. The tool is the artifact a reviewer reruns.
* `principle-encode-lessons-in-structure`.
* `principle-exhaust-the-design-space`. A novel interaction or architectural decision with no precedent. Build 2-3 competing prototypes and compare before committing.
* `principle-experience-first`. Product, UX, or feature-scope tradeoffs. Choose user delight over implementation convenience.

* `principle-laziness-protocol`. Refactoring, sizing a diff, or tempted to add abstractions, layers, or signal threading. Bias to deletion and the smallest change that solves the problem.
* `principle-foundational-thinking`. Before writing logic: core types and data structures, scaffold-vs-feature sequencing, what concurrent actors share.
* `principle-redesign-from-first-principles`. Integrating a new requirement into an existing design. Redesign as if it had been foundational from day one.
* `principle-attack-the-premise`. Two or more fixes that share one premise have failed the same gate. Write the premise down and test it directly before you write another fix that assumes it.
* `principle-subtract-before-you-add`. Sequencing an addition, refactor, or rewrite. Remove dead weight first, then build on the simpler base.
* `principle-minimize-reader-load`. Reviewing or shaping code that's hard to trace. Count layers and hidden state, collapse one-caller wrappers, shrink mutable scope.
* `principle-outcome-oriented-execution`. Planned rewrites and migrations with explicit phase boundaries. Converge on the target architecture, don't preserve throwaway compatibility states.
