# Happy skills

A collection of instruction skills for Happy Agent.

| Skill | Purpose |
|-------|---------|
| how | Explain subsystem behavior, ownership, and architecture; critique when requested. |
| bro | Restate the previous answer in plain language. |
| visualize | Turn an explanation into a visual artifact and show it with the Happy design system. |
| blast-radius | Investigate downstream breakage and verify safety assumptions with real code. |
| typescript-best-practices | Model TypeScript domains and validate boundaries with repository conventions. |
| unslop | Edit prose for clarity, concrete language, and a natural voice. |
| sync-to-main | Commit workspace work, rebase on main, and push without force. |
| happy-mode | Apply a principled execution style with 24 principles and 23 task playbooks. |

Copy the desired folders from `skills/`, including their `references` directories, into `~/.agents/skills`. Alternatively, configure the repository's `skills/` folder as an extra skill directory in Happy's user or project `happy.toml`:

```toml
[skills]
directories = ["/absolute/path/to/repository/skills"]
```

Prefer an absolute path. Relative paths in project configuration resolve from the project root. This `skills/` folder is a distribution layout; its skills do not become active project skills merely by cloning the repository. Happy also discovers project skills in `.agents/skills`.

`bro`, `visualize`, `blast-radius`, `sync-to-main`, and `happy-mode` use `disable-model-invocation: true` and are manual commands: `/bro`, `/visualize`, `/blast-radius`, `/sync-to-main`, and `/happy-mode`. The other skills can be selected by a matching request or invoked by name. The global instructions treat "Sync To Main" and "lfg" as equivalent user requests; they are not additional registered commands. Publishing follows Happy's environment permission reviews.

Copy the complete `happy-mode` folder to retain its principles and playbooks.
