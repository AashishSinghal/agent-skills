# agent-skills — Project Directives

A small collection of reusable agent skills, meant to be copied or installed
into any agent's skills directory and used across repositories and CLIs. There
is no code, build, package manager or test suite. Every file is Markdown.

## Prime directives

- **A skill is a folder with one `SKILL.md`.** The folder name and the `name`
  field in the frontmatter must match exactly (kebab-case).
- **The `description` field drives triggering.** Agents read it to decide when
  to load the skill, so it must say what the skill does and when to use it
  ("Use when ..."). Treat edits to it as behaviour changes.
- **Skills must stay portable.** Write for any agent and any repo. Do not tie a
  skill to one CLI's tool names, one machine's paths or one project, unless the
  skill is explicitly about that thing.
- **Keep `README.md` in sync.** Every skill folder gets an entry under
  `## Skills` in the README; removing or renaming a skill updates it too.

## Layout

```
README.md                    what the repo is, list of skills, usage
explain-code-flow/
  SKILL.md
site-visit-counter/
  SKILL.md                   analytics + shared visit counter for the owner's sites
```

`explain-code-flow` explains a code path or change in beginner-friendly terms,
grounded in the actual codebase, with cross-language mapping (for example Go to
JavaScript).

## Adding a new skill

1. Create `<skill-name>/SKILL.md` at the repo root (kebab-case folder name).
2. Start the file with YAML frontmatter containing exactly the two fields the
   existing skill uses:

   ```markdown
   ---
   name: <skill-name>
   description: <What it does, in one or two sentences>. Use when <trigger conditions>.
   ---
   ```

3. Follow with the body, using the structure of `explain-code-flow/SKILL.md`
   as the template:
   - `# <Title Case Name>` and one line on when to use it
   - when it is especially useful (bullets)
   - `## Goal`: what the output must achieve
   - `## Workflow`: numbered steps the agent follows
   - `## Output Shape`: the default structure of the answer
   - `## Style Rules`
   - optional sections for special cases (for example `## When Creating a Repo Doc`)
   - `## Example Prompt Patterns`: sample requests that should trigger it
   - `## Good Outcome`: what success looks like for the user
   Drop sections that do not apply. Do not pad.
4. Add an entry to `README.md` under `## Skills`: the skill name in backticks
   on one line, then an indented one-sentence summary.
5. If the skill needs extra files (scripts, references, templates), keep them
   inside the skill's own folder and reference them by relative path from
   `SKILL.md`. No existing skill does this yet.

## Running, building, testing

Nothing to run. To check a skill, copy its folder into an agent's skills
directory and invoke it with a prompt like the one in `README.md`:

`Use explain-code-flow to walk me through this service flow like I'm a JavaScript developer.`

Confirm the agent picks it up from the description and follows the workflow.

## Configuration

None. No environment variables, no config files.

## Conventions

- Plain, direct Markdown. Imperative instructions to the agent ("Read the
  primary entrypoint...", "Prefer examples over abstractions").
- Examples inside skills should be concrete (real-looking file names and
  flows), not generic placeholders.
- Commit messages use Conventional Commits, as in the only commit so far:
  `feat: add explain-code-flow skill` (a new skill and its README entry
  landed together in that commit).

## Current state and next steps

- One skill (`explain-code-flow`), one commit, on `main`, pushed to
  `git@github.com:AashishSinghal/agent-skills.git`.
- No roadmap or planned skills are recorded in the repo. Ask the user before
  inventing a structure beyond what is described here (for example an index
  file, a manifest or install scripts).
