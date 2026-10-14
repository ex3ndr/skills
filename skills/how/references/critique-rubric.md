# Architecture critique rubric

Assess the following against actual use cases and repository constraints:

- Ownership: is each decision and state owned in a clear place? Are callers relying on hidden implementation details?
- Invariants: can invalid states be represented or reached? Where are external inputs checked?
- Coupling: does an ordinary change require synchronized edits across unrelated layers or duplicated contracts?
- Lifecycle: are startup, cancellation, teardown, retries, and concurrent access well defined?
- Failure handling: can partial failure lose data, repeat effects, or hide actionable errors?
- Observability and validation: can failures be diagnosed and important behaviors exercised with real code?
- Change cost: does the design solve today's requirements at a reasonable complexity, and what concrete future change exposes a limitation?

Every finding needs a reachable scenario, evidence, consequence, and proportionate improvement. Consider counterexamples and intentional tradeoffs. Missing evidence is an investigation gap, not proof of a defect. Rank confirmed findings by impact and likelihood, not stylistic preference.
