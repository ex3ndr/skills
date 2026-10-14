### Opening a PR

Used at delivery when the user has authorized a PR. Read-only and local-only tasks end with their result instead. Prefer Happy's dedicated workspace and project tools for workspace lifecycle.

**Worktree.** Use the task's dedicated Happy workspace. Independent writers get separate workspaces or owned branches through available workspace/subtask tools. Preserve unrelated and uncommitted work. Never reset or discard it to prepare a PR; ask its owner to isolate or integrate it. Read applicable repository instructions.

**Commits.** Commit liberally. Rebase into small, ordered commits before opening PRs. Each commit is a future PR: landable, ordered to tell the story. Amend when the fix belongs in a just-made commit. New commit when separable. Commit bodies don't restate the subject.

**PRs.** Inspect the diff before commit for unrelated changes, speculative layers, weak types, and comments narrating obvious code. Write titles, descriptions, and commit bodies for the reviewer in plain language, preserving the evidence and tradeoffs. Follow repository templates and conventions.

**Titles.** Conventional Commits `type(scope): subject`, no trailing period. Type such as `feat`, `fix`, `docs`, `refactor`, `test`, `chore`, or `perf`. Scope is the area, such as `skills` or `happy-mode`. Subject is short, imperative, and names a real symbol when one carries the change, for example `fix(skills): clarify the PR status trigger`.

**Descriptions.** The PR body is a one-minute briefing, not a lab notebook, for a reviewer who has the diff. It says why the change exists, what it leaves out, what it could break, and how you proved it works. Write short, simple sentences with few identifiers, no walls of text. The body becomes the squash commit body, so keep that commit under about 40 lines. Use `##` headings, not bold lead-ins, in this order, and drop a section with nothing to say:

- `## Why`: the problem and approach, 1–3 short sentences. No SHAs, rebase genealogy, or "based on main" preamble.
- `## What changed`: 1–3 short bullets. Name a real symbol or path only when it carries the change. Name both sides of a rename or retarget.
- `## Scope`: always. 1–3 short items on what it covers and what it deliberately leaves out (a follow-up, a known gap). No symbols, paths, or file-by-file essay.
- `## Tradeoffs`: only rejected alternatives a reviewer would otherwise ask about. Skip when there was no real choice.
- `## Blast Radius`: 1–2 sentences on who or what it touches and why that is safe or risky. If main is red, state the cost of leaving it red.
- `## Verification`: 1–3 bullets, each a real run path and its outcome. Perf: one primary number with its unit, `before → after`, and a link to the comparison or verification directory for the rest. No sample-size methodology, verification-team recitals, or metric tables.

After the sections, attach videos or screenshots when they prove a claim. Full SHAs, verification or comparison lane recitals, lever-correction essays, file-by-file checklists, and "CLEAN" verdicts go in a linked artifact, not the body.

**Forge.** Resolve the forge before the first PR operation and keep that choice for create, edit, view, watch, and merge. Use available GitHub integration tools or normal GitHub CLI (`gh`) operations consistently.

**Built-in PR tool.** When the run provides a built-in PR tool, create, edit, retarget, and mark ready through it, never through a forge CLI. A CLI-made PR misses what the tool tracks. Use the resolved forge for everything the tool does not cover, and for every PR operation when the run has no such tool.

**Size and stacks.** Prefer five narrow PRs to one large PR. A stack is a base-branch chain. The root PR targets trunk. Each child branch rebases onto its parent's exact tip and its PR targets the parent branch. As a parent merges, move its child onto trunk per `shipping.md` step 4. Without a built-in PR tool, per the resolved forge, create a child with `gh pr create --base <parent-branch>`, and retarget one with `gh pr edit <pr> --base <parent-branch>`. Branch from trunk only for independent work. Rebase on trunk before substantial stack work.

**Readiness.** Open every PR ready, never as a draft. A built-in PR tool can default to draft, so set its supported readiness field deliberately according to the current tool schema. With `gh`, use `--draft` only when a draft is intended. If a PR intended as ready still opens as a draft, mark it ready through the PR tool, or run `gh pr ready <number>` according to the resolved forge. Run `gh pr view <number>` before you refer to PR status.

**Babysit.** Opening a PR does not start a babysit. Post the URL and keep building. Finish the phase or stack first. Run a separate babysit pass only when the user asks for one after the whole stack exists. A babysit for each new PR stalls the build and spends checks on commits later waves restart. Push back when feedback drifts from intent.

A subagent that opens a PR reviews its diff, performs the required checks and any independent review, and posts the URL. It returns to the parent without babysitting, unless it is an Autopilot-full or Autopilot-stack owner. That owner's brief assigns the babysit loop and is the ask `babysit.md` waits for. The owner starts the loop after its code-ready report, reports merge-ready or STACK-READY per its playbook, and is exempt from the rules here and in `babysit.md` that hold babysitting until a whole stack is built.
