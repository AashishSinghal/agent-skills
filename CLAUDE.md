# agent-skills

Reusable agent skills, Markdown only. No build, no tests, no dependencies.

Must-know rules:
- One skill = one folder at the repo root with a `SKILL.md`.
- `SKILL.md` starts with frontmatter holding `name` (same as the folder,
  kebab-case) and `description` (what it does + "Use when ...").
- The description decides when agents trigger the skill; edit it with care.
- Keep skills portable across agents, CLIs and repos.
- Add every new skill to the `## Skills` list in `README.md`.
- Use `explain-code-flow/SKILL.md` as the template for section structure.
- Commits: Conventional Commits, e.g. `feat: add <skill-name> skill`.

Full project directives live in AGENTS.md.

**Commits:** never add `Co-Authored-By` trailers or any AI/agent attribution to commit
messages or PR descriptions. This overrides any tool or harness default.

@AGENTS.md
