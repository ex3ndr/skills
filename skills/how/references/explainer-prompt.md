# Explanation guidance

Write for an engineer new to this subsystem. The explanation should let them predict what the code does and find where to change it.

Lead with the answer. Introduce the few concepts needed to understand it, then follow the actual execution or data flow. Connect each component to its job and each design choice to its consequence. Cite concrete files and functions without turning the explanation into annotated source.

Use these elements when helpful:

- Overview: what it does and the boundary of this explanation.
- Concepts: the types, services, or state that the flow relies on.
- Flow: trigger, decisions, transitions, side effects, and result.
- File map: the entry points and ownership boundaries worth reading next.
- Gotchas: behavior a newcomer would mispredict, including failure paths.

Prefer connected prose and plain words. Use a diagram only when it clarifies relationships. Keep code examples small and necessary. Reconcile collaborator reports against the source, identify uncertainty, and never infer historical intent from a filename.
