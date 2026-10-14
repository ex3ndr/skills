---
name: how
description: "Explain how a subsystem works, trace runtime behavior, or answer code ownership and layering questions. Use for code walkthroughs, architecture explanations, and architecture critiques."
---

# How

Build a working mental model from the actual code. Explain what starts the flow, what happens, where data goes, and which decisions matter. Work within the user's question; explanation alone does not authorize code changes.

## Establish the scope

Interpret the question and state a reasonable scope when it is ambiguous. For a bare `/how`, use the current conversation or recent changes to choose a target. Ask only when there is no useful target to infer.

Read applicable repository instructions. Start with first-party code using `rg`, file reads, and available read-only tools. Exclude dependencies and generated output until a specific unresolved behavior requires inspecting them. Trace entry points, callers, state changes, side effects, and outputs. Separate evidence from inference, and cite files and functions.

## Investigate

Handle narrow questions yourself. For a substantial subsystem with independent investigation angles, use Happy's available collaboration tools only when delegation improves the answer. Discover their current schemas through the tool catalog if needed; do not assume a provider-specific API.

Assign two to four bounded angles such as data model, request flow, configuration, or failure handling. Follow current delegation and nesting limits. Request read-only collaborators when the tool supports it; otherwise give explicit no-edit instructions. Select model and effort from the current session's available choices when the tool requires them. Continue your own independent investigation while they work, use completion notifications, and follow up with existing collaborators to resolve gaps.

Use [the explorer prompt](references/explorer-prompt.md) for assignments. Each needs a question, source boundaries, entry points, and an evidence requirement. Reconcile disagreements against the code before presenting the result.

## Explain

Use [the explanation guidance](references/explainer-prompt.md). Lead with what the subsystem does and why its design matters. Introduce only the concepts needed to follow the flow. Walk from trigger to outcome in plain language, then identify useful files and non-obvious behavior. Adapt the length to the question; a small utility does not need an architecture report.

Keep confirmed behavior, inferred intent, and unknowns distinct. When motivation matters, read relevant comments, tests, and available commit or PR history rather than inventing a rationale. Report evidence gaps directly.

## Critique when requested

Explain first. Then assess ownership, invariants, coupling, failure handling, and change costs with [the critique rubric](references/critique-rubric.md). For a broad design, independent read-only collaborators can assess distinct angles using [the critic prompt](references/critic-prompt.md); simple designs need no fanout.

Judge the findings yourself. Group them as act now, consider, low priority, or dismissed, and explain the evidence and tradeoff. Present the explanation before the critique. Use available Happy collaboration tools for analysis; run external review services only when the user explicitly requests them.
