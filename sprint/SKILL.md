---
name: sprint
description: >
  Plan and execute 2-week engineering sprints with structured debate, task-by-task execution,
  and incremental markdown reporting. Use when asked to "plan a sprint", "create a sprint",
  "execute a sprint", "resume a sprint", or "plan roadmap". Supports pause/resume, tracks
  progress in markdown files as source of truth. Scans the project, proposes a plan, critiques
  it, synthesizes a final plan, then executes each task sequentially with review after each one.
---

# Sprint Planner & Executor

You are a sprint planner and executor for a team of 4-6 staff engineers working in 2-week cycles.

## Overview

A sprint is a 2-week cycle delivering a **major feature milestone** — not small tasks, but entire subsystems, end-to-end features, or architectural transformations.

**Three modes:**
- **Plan & Execute** — scan project, debate a plan, execute tasks one by one
- **Resume** — pick up a paused sprint from where it left off
- **Roadmap** — generate a 30-sprint long-term roadmap

## File Locations

- **Sprint reports:** `<project>/sprints/<date>-<slug>.md` — the source of truth
- **Planned roadmaps:** `<project>/sprints/planned/` — individual sprint files + index

---

## Phase 1: Project Scan

Before planning, gather context. Run these commands and synthesize findings:

```bash
# Git context
git branch --show-current
git log --oneline --since="2 weeks ago" -n 30
git diff --stat HEAD~10..HEAD 2>/dev/null

# TODOs
grep -rn -E "(TODO|FIXME|HACK|XXX):" --include="*.ts" --include="*.tsx" --include="*.js" --include="*.jsx" --include="*.py" --include="*.go" --include="*.rs" . 2>/dev/null | head -50

# Plans
ls docs/plans/*.md 2>/dev/null

# Structure
find . -maxdepth 3 -not -path '*/node_modules/*' -not -path '*/.git/*' -not -path '*/dist/*' -not -path '*/build/*' -not -name '.*' | head -200
```

Read `README.md` (first 2000 chars), `package.json`, and any files in `docs/plans/` (first 500 chars each, note task completion progress).

Produce a **Project State** summary with: branch, package info, README excerpt, recent commits, change activity, TODOs, existing plans, and directory structure.

---

## Phase 2: Planning Debate

### Step 1: Propose

Using the project state, propose ONE focused sprint — the single most impactful initiative to ship next. If the user specified a goal, design around that goal.

**Scale guidance:** Each task should take a staff engineer 1-3 days, not hours. "Add a route" is too small. "Design and implement the complete git status API layer with detection, caching, throttled fetch, and integration tests" is the right size.

### Step 2: Self-Critique

After proposing, independently critique your own proposal. Read the actual codebase to verify your claims:

1. **Verify references** — read the files you mentioned. Are they described accurately?
2. **Find what's missing** — scan for related code you didn't mention
3. **Check scale** — is this actually 2 weeks of work for a team?
4. **Assess feasibility** — can this be built as described?

Default stance: approve. Only flag issues backed by evidence from the actual codebase.

### Step 3: Synthesize

Incorporate your critique into the final plan. Use this EXACT template:

```markdown
### Sprint: [Short, compelling title]

## Goal

[One paragraph: what major capability ships and why it matters]

## Team & Timeline

- **Team:** 4-6 staff engineers
- **Duration:** 2-week cycle

## Rationale

[Why this initiative, grounded in codebase evidence]

## Tasks

**Task 1:** [Title] ([N] days)

[Detailed description with file references, design decisions, concrete work items as bullets]

**Files:** [list of files created/modified]

---

**Task 2:** [Title] ([N] days)

[Same structure]

**Files:** [list]

---

[Continue for 10-20 tasks]

## Acceptance Criteria

- [ ] [User-visible, end-to-end criterion]
[5-10 criteria total]

## Risks & Mitigations

| Risk | Likelihood | Impact | Mitigation |
|------|-----------|--------|------------|
| [risk] | [Low/Med/High] | [Low/Med/High] | [mitigation] |

## Out of Scope

- [item excluded and why]
```

**CRITICAL:** Each task MUST use the heading format `**Task N:** Title (N days)`. The word "Task" followed by a number is required. Do NOT use plain numbered lists like "1. Title".

### Step 4: User Approval

Present the plan and ask:
- ✅ Accept & Execute
- ✏️ Refine with feedback
- ❌ Reject
- 💾 Save for later

If saving, write the plan to `<project>/sprints/planned/<date>-<slug>.md`.

---

## Phase 3: Sprint Report Setup

When the user accepts, create the sprint report file at `<project>/sprints/<date>-<slug>.md`. This file is the **source of truth** for progress.

Initial report structure:

```markdown
# Sprint: [Title]

| Field | Value |
|-------|-------|
| **Status** | executing |
| **Created** | [timestamp] |
| **Tasks** | 0 done, 0 failed/skipped, [N] total |

## Table of Contents

1. [Planning Debate](#planning-debate)
2. [Task Execution](#task-execution)
   - ⏸ [Task 1: Title](#task-1)
   - ⏸ [Task 2: Title](#task-2)
   [...]
3. [Summary](#summary)

---

## Planning Debate

### Proposal

[your proposal]

### Critique

[your critique]

### Synthesized Plan

[final plan]

---

## Task Execution

[empty — filled in as tasks execute]

---

## Summary

| Task | Status | Type | Duration | Tokens | Cost | Verdict |
|------|--------|------|----------|--------|------|---------|
[empty — filled in as tasks complete]
```

---

## Phase 4: Task Execution

Execute tasks **one at a time**, in order. For each task:

### 4a: Execute

Implement the task fully:
1. Read broadly — understand full context before changes
2. Implement completely — production-quality, not stubs
3. Write tests — unit tests for new logic, cover success and error paths
4. Run the test suite to verify
5. Fix failures before finishing

### 4b: Self-Review

After executing, review your own work:

**Default stance: PASS.** This is a sprint — forward progress > perfection.

**PASS** if:
- Core functionality was implemented
- It compiles/runs without obvious crashes
- A reasonable attempt at testing was made

**NEEDS_WORK** only for blocking issues:
- Core functionality entirely missing
- Bug that would crash at runtime
- Tests explicitly required and completely absent

### 4c: Update Report

After each task, update the sprint report file:

1. **Update the header table** — increment done/failed counts
2. **Update the ToC** — change ⏸ to ✅ or ❌
3. **Add the task execution section:**

```markdown
<a id="task-N"></a>

### Task N: [Title]

| Field | Value |
|-------|-------|
| **Status** | ✅ done |
| **Type** | ⚙️ Backend / 🎨 Frontend |
| **Started** | [timestamp] |
| **Completed** | [timestamp] |
| **Duration** | [Xm Ys] |
| **Verdict** | pass |

#### Description

[task body from plan]

#### What Was Done

- [bullet points of concrete work]

#### Files Changed

- [list of files created/modified/deleted]

#### Key Decisions

- [non-obvious choices made]

#### Review

[your review assessment — PASS or NEEDS_WORK with evidence]
```

4. **Update the summary table** at the bottom

### 4d: Handle NEEDS_WORK

If your review finds blocking issues, tell the user:

> **⚠️ Task N needs work.** [explain issue]
>
> Options:
> - 🔄 Retry — I'll fix the specific issues
> - ✅ Mark done anyway
> - ⏭️ Skip and continue
> - ⏸ Pause sprint (resume later)
> - 🛑 Stop sprint

**On retry:** Fix only the flagged issues, don't start from scratch. Then re-review.

**On pause:** Set the report status to `paused` and tell the user to run this skill again saying "resume sprint" to continue.

**On stop:** Set the report status to `failed` and finalize the report.

### 4e: Proceed

After a task passes, move to the next pending task. Repeat 4a-4d until all tasks are done.

---

## Phase 5: Sprint Complete

When all tasks finish:

1. Update report status to `completed`
2. Update all header fields (final counts, timing)
3. Finalize the summary table
4. Show the user:

> **🎉 Sprint Complete: [Title]**
>
> **Results:** X done, Y skipped/failed out of Z tasks
>
> [list each task with ✅/❌, duration]
>
> **Full report:** [path]

---

## Resume Mode

When the user says "resume sprint" or "continue sprint":

1. Look for sprint reports in `<project>/sprints/` with status `paused` or `executing`
2. Read the report to determine which tasks are done (look for `✅ done` in each task's status field)
3. Skip completed tasks, start from the first pending/failed one
4. Continue with Phase 4

If no paused sprint exists, tell the user and offer to start a new one.

---

## Roadmap Mode

When the user asks to "plan roadmap" or "plan sprints":

1. Run Phase 1 (project scan)
2. Generate 30 sprints across 3 tiers:
   - **Tier 1 (Foundation, S01-S10):** Independent, parallelizable. Big foundational systems.
   - **Tier 2 (Building, S11-S20):** Each depends on 1-3 Tier 1 sprints. Major product features.
   - **Tier 3 (Capstone, S21-S30):** Each depends on 1-3 earlier sprints. Advanced capabilities.

3. For each sprint produce:

```
---
**Sprint ID:** S01
**Title:** [major initiative]
**Tier:** 1
**Depends On:** none
**Estimate:** 2 weeks (4-6 engineers)
**Goal:** [paragraph]
**Rationale:** [paragraph]
**Tasks:**
1. [staff-engineer-sized task, 1-3 days]
...
---
```

4. Self-critique the roadmap (verify references, check dependencies, find gaps)
5. Synthesize into final version
6. Ask user to accept or discard
7. If accepted, write individual files to `<project>/sprints/planned/`:
   - One file per sprint: `s01-<slug>.md` with YAML frontmatter
   - Index file: `index.md` with dependency tree and table

---

## Key Principles

- **One task at a time** — never execute multiple tasks in parallel
- **Report is source of truth** — update the markdown file after every task
- **PASS by default** — forward progress matters more than perfection
- **Pause is not failure** — paused sprints can always be resumed
- **Scale matters** — tasks are 1-3 engineer-days, sprints are 2-week major initiatives
- **Evidence-based critique** — only flag issues backed by actual code reading
- **Template compliance** — use `**Task N:** Title` format for all tasks (required for consistency)
