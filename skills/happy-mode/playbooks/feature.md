### Feature

**You own the design. Plan, review, verify.** Delegate substantial independent implementation when useful. Simple work stays inline. Stay responsible for design and review.

1. Trace the affected subsystem.
2. Compare boundary designs with bounded independent exploration.
3. Write the throughput checkpoint as four todo items. A dimension that genuinely does not apply (single file, no fan-out) keeps its item with `n/a: <reason>` rather than being dropped:
   - **Blocking first steps.** Gates run before fan-out.
   - **Independent workstreams.** Disjoint files, services, or layers parallelize. Shared writes serialize.
   - **Shared mutable state.** Default to splitting the target (the **separate-before-serializing-shared-state** principle resource). Serialize only for real invariants.
   - **Smallest safe decomposition.** If one worker is best, name why.
4. Delegate code-writing to a subagent using an available model selected for the task with a specific scope (file paths, named data shape and its organizing structure per **Model the Domain**, a state machine over scattered booleans, a table/registry over branching, a typed model over repeated shape assumptions, chosen before the delegate writes logic, and success criteria). Pick model and effort from the current Happy catalog when the tool requires them, per the Subagents section. When the implementation admits multiple valid shapes (error handling, abstraction layer, test structure), delegate via the core's isolated design-comparison procedure instead so the runners surface the alternatives and the cross-judge guards the pick. Scale the comparison to the decision. Preserve independent review where it adds evidence without mandating fanout for a trivial change. A subagent forbidden to spawn satisfies this by owning the diff directly with the same review separation. No "standing by" reply that waits on a nested agent. Comments per **Comments**. Surgical edits, re-ground against the source for upstream-derived files. Port shared-primitive improvements to all consumers and verify each. Commit liberally.
5. Verify on the matching surface. "Inconclusive" or wrong-surface is not a pass. Flag it.
6. Rebase into small, ordered commits. Stack follow-ups.
   Use the **sequence-verifiable-units** principle resource, building, verifying, and committing each small unit before the next.
7. If the design is contested, independent critical review before shipping.
8. Run **Opening a PR**.

Code-coupled work (one feature, one migration) goes to a single owner with the checkpoint inline. That owner fans out internally after the blocking phase. Parent-level fan-out is for slices that produce independent artifacts (audits, cross-subsystem investigations, competing experiments). Rewrite the checkpoint at phase boundaries. Follow up with the existing owner when its context or workspace remains useful; give any replacement consolidated scope.

**Reply:** what you built, what you chose and why, the throughput checkpoint, open decisions. Tables for design alternatives.
