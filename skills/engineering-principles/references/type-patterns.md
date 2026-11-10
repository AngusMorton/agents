# Type patterns

Open this file when you design a new type or review a function signature. `SKILL.md` states the rules. This file gives the form of each rule in Kotlin, Swift, and TypeScript, and the patterns that don't fit in `SKILL.md`.

## The form of each rule in each language

| Rule | Kotlin | Swift | TypeScript |
| --- | --- | --- | --- |
| A closed set of alternatives | `sealed interface` with `data object` and `data class` cases | `enum` with associated values | A union of object types that share a literal `kind` field |
| A distinct type for an ID or a code | `@JvmInline value class` | A `struct` that wraps the raw value | A branded type, such as `string & { readonly __brand: "UserId" }` |
| An exhaustive match | `when` used as an expression, with no `else` | `switch` with no `default` | `switch` with a `default` that assigns the value to a `never` variable |
| Casts to avoid | `!!` and an unchecked `as` | `!`, `as!`, and `try!` | `as`, `any`, and the non-null `!` |
| A parse at the boundary | A `kotlinx.serialization` class, mapped to a domain type | A `Decodable` type, mapped to a domain type | A `valibot` or `zod` schema |

In C#, wrap an ID in a `readonly record struct`, turn on nullable reference types, and avoid the null-forgiving `!`.

## Patterns

- **Replace a boolean and a nullable with one type.** `completed: Boolean` with `completedAt: Instant?` lets `completed = true` compile with no time. Keep only `completedAt`, or model the two cases as a closed type.
- **Build the type from the values you want.** Don't take a loose type and restrict it with checks. A non-empty list is a first element and a list of the rest, not a list with a length check.
- **Choose a structure before you add a branch.** Use a state machine in place of booleans and lifecycle checks spread across files. Use a map or a lookup table in place of the same `when` repeated in several files. Use one typed object in place of separate parameters that callers always pass together.
- **Derive a type from the schema that owns the shape.** When a SQLDelight `.sq` file, an API schema, or the design tokens define a shape, use the generated type. Don't write a second copy by hand.
- **Tighten a type only where code would fail at runtime.** A runtime assertion, a null check, or a "this never happens" error marks a type that is too loose. Move that check into the type, then stop. A function that sums a list has an answer for an empty list, so it takes a plain list. A function that returns the first element has no answer, so it takes a non-empty list.
- **Keep plain code when plain code is clear.** Add a structure only when it removes branches, duplicated rules, or states that can't happen. Code that is local and unlikely to grow doesn't need one.

## Questions to ask of a type

- Can you write a comment that explains when a combination of fields is valid? If yes, the type is too loose. Split it into cases.
- Do two parameters share a primitive type but mean different things? Give each one its own type.
- Where did this `!!`, `as!`, or `any` come from? Trace it to the boundary and parse there.
- If someone adds a case next month, does the build fail at each place that must handle the case?
- Does this type copy a shape that another file owns? Use the type from that file.
- Does the tighter type prevent a runtime failure, or does it only describe the data more exactly? If nothing would fail, keep the plain type.
