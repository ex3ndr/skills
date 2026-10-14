---
name: blast-radius
description: "Investigate what a change could break beyond its diff and prove the key safety assumptions with real code. Use when the user invokes /blast-radius or requests a blast radius assessment."
disable-model-invocation: true
---

# Blast radius

Find reachable breakage beyond the changed lines. A caller list is a starting point; the result should identify the facts the change depends on and the evidence for them.

1. Establish the change and baseline from the user's request, workspace diff, or named commit or PR. Read applicable repository instructions, changed symbols, tests, and relevant history. Use available Git or integration tools to inspect commits and PR context; distinguish documented intent from inference.
2. Identify the one or two assumptions that make the change safe. State each precisely, such as whether an operation can delete live state or repeat a side effect.
3. Trace what symbol search misses: serialized data, database columns, wire contracts, feature flags, asynchronous ordering, teardown, retries, and downstream consumers. Inspect actual dependency source, pinned versions, and local patches when behavior depends on them.
4. Assess reachable scenarios. For each risk, identify the trigger, affected code or contract, likelihood, consequence, and cheapest useful verification. Keep confirmed risks separate from scenarios you checked and cleared. An empty search is evidence only within its stated scope.
5. Prove the central assumptions by running the real code when practical. Prefer a focused existing test, a small reproduction calling the shipped implementation, or an actual app flow. Record the command and result. Stay within the authorized task and environment boundaries; if execution requires an unavailable service or would mutate data outside scope, report what remains unproven.
6. For substantial independent concerns, use available Happy collaboration tools to request bounded investigations of different angles. Discover schemas when needed, choose from current available models if required, and request read-only mode when supported. Respect delegation limits and continue independent work while collaborators investigate. Reconcile their evidence yourself. Do not run external review services unless the user explicitly asks.

## Evidence strength

For each safety assumption, report the strongest evidence reached:

1. Hypothesis only.
2. Supported by specific source lines.
3. Failure path traced and ruled out within a stated scope.
4. Exercised by a test or reproduction calling real code.
5. Reproduced in the running application.

An assumption below level four is unproven by execution. Describe the limit without treating it as settled. Use the cheapest meaningful check; do not add tests that merely mirror implementation.

## Deliver the assessment

Explain what changed, the central safety assumptions and their evidence, confirmed risks, cleared scenarios, and the next useful check before merging. Cite concrete files and symbols. Include relevant commands and results, and state any blocked verification. Keep private data out of outputs and do not publish the assessment without authorization.
