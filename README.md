# agent-skills

Reusable agent skills that can be used across repositories and CLIs.

## Skills

- `explain-code-flow`
  Explains how a code path or change works in beginner-friendly terms, with a focus on real execution flow, file responsibilities, concrete examples, and cross-language mental mapping such as Go to JavaScript.
- `site-visit-counter`
  Adds Vercel Web Analytics and the shared visit counter (one Upstash-backed API on aashishsinghal.com) to a new project on aashishsinghal.com or one of its subdomains.

## Usage

Copy or install a skill folder into your agent's skills directory, then invoke it in prompts. Example:

`Use explain-code-flow to walk me through this service flow like I'm a JavaScript developer.`
