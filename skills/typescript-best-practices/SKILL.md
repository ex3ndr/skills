---
name: typescript-best-practices
description: Use when reading, writing, or refactoring TypeScript and TSX to improve domain modeling, boundary validation, narrowing, and maintainability.
---

# TypeScript best practices

Read the applicable repository instructions and follow its existing compiler settings, schema tools, framework, and testing conventions. Use the simplest types that preserve the real invariants; strengthen a type when its loose form forces unsafe assertions or hidden failure paths.

| Practice | Apply it when |
|----------|---------------|
| Discriminated unions | Model mutually exclusive states with a shared literal field, rather than contradictory booleans and optional fields. |
| Branded primitives | Use validated brands when ordinary strings or numbers from different domains could be mixed up. Follow the repository's convention. |
| Constructive modeling | Use representations such as non-empty tuples or pairs when they express the invariant; validate numeric and external constraints at creation. |
| Total operations | Keep ordinary arrays when empty inputs have a meaningful result. Otherwise model non-empty inputs or explicitly return absence. |
| `unknown` at boundaries | Validate external inputs once into a named domain type, using the repository's schema tools when available. Trust validated types internally. |
| Narrowing before assertions | Prefer discriminants, `in`, `typeof`, and `instanceof`. Type guards must check their claims. An assertion documents a proof; it cannot replace one. |
| Exhaustive handling | Use `never` or the repository's exhaustiveness helper so new variants produce a compiler error. |
| `satisfies` | Check literal configuration shapes without widening useful literal information. |
| Derived types | Derive from schemas and existing types with `Pick`, `Omit`, `Parameters`, `ReturnType`, `Awaited`, or `typeof` before duplicating interfaces. |
| Clear arguments | Prefer named object arguments when ordering is easy to confuse. Respect stable APIs and measured allocation-sensitive paths. |
| Real behavior checks | Run relevant type checks and tests. Prefer real framework or library behavior to mocks; mock only unavailable boundaries. |
| Useful diagnostics | Follow the repository's structured logging conventions and include actionable context without exposing secrets. |

See [patterns and examples](references/patterns.md). Adapt examples to local types and conventions; they are illustrative, not standalone application code. Avoid unrelated refactors and invented abstraction layers. For a small change, run the checks it needs rather than building a new test harness.
