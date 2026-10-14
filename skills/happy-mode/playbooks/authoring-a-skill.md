### Authoring or modifying a skill

**You own the skill's voice.**

1. Author YAML frontmatter followed by Markdown for Happy. Keep only `name`, `description`, and optional plain-boolean `disable-model-invocation`. Names match `^[a-z0-9-]+$`, have at most 64 characters, and contain no leading, trailing, or doubled hyphen. A trimmed description has 1–1024 characters. Use the manual flag for user-only procedures. Read current Happy skill documentation when available.
2. Validate the skill: frontmatter has `name` and `description`, referenced files exist, cross-skill links resolve.
3. Test cases if structural. Skip if subjective.
4. Run **Opening a PR**.

When in doubt, delete. Keep only prose that changes a decision. Tell it to do the thing and skip the reason. Explain only when the rule is confusing without one. Match tone to scope. Point at structural sources (types, READMEs, config) per the **encode-lessons-in-structure** principle resource. Delegate to other skills by path. Don't restate. A workflow you keep hitting but isn't captured → propose a new skill.

**Reply:** summary of the skill, key design decisions, validation notes.
