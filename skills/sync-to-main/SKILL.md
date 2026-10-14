---
name: sync-to-main
description: "Commit all workspace work, rebase on main, and push to main without force. Use when the user invokes /sync-to-main, says Sync To Main, or says lfg."
disable-model-invocation: true
---

# Sync To Main

"Sync To Main" means: commit all the work, rebase on main, and push to main without force, retrying on conflict. "lfg" means sync to main.

`/sync-to-main` is the Happy manual command. "Sync To Main" and "lfg" are equivalent user wording under the global instructions, not additional registered commands. An explicit request authorizes this workflow without an extra confirmation step. Respect environment permission reviews and their denials. Use the available Git and shell tools with their current schemas; request reviewed access for a necessary command when the sandbox blocks repository metadata writes or network access.

1. Inspect the workspace status and repository instructions. Include all workspace changes and untracked intended project files; keep ignored scratch files and secrets out of the commit. Never discard existing work.
2. Run appropriate checks, stage all intended project changes, and commit with a concise message describing the work. If there are no changes, continue with the existing commit.
3. Fetch origin and rebase the current branch on the latest `origin/main`. Resolve conflicts by preserving the intended changes and incorporating main's updates, then continue the rebase. If a conflict requires a product decision you cannot infer, ask the user while preserving the work.
4. Run checks appropriate to the resulting changes. Fix failures and commit any necessary fixes before publishing.
5. Push with `git push origin HEAD:main`. Never force-push. If main advances and the push is rejected, fetch origin, rebase on the updated `origin/main`, resolve conflicts, repeat affected checks, and retry the normal push.
6. Verify the remote main SHA matches local HEAD and inspect the final workspace status. Report the pushed commit SHA, checks performed, and whether the workspace is clean.

Retry Git conflicts and a remote branch advancing as described above. An environment permission denial is a separate blocker: stop that action and explain the reviewer’s reason. Do not bypass the denial or retry without the explicit authorization required by the environment.
