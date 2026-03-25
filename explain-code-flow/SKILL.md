---
name: explain-code-flow
description: Explain how a code change or code path works in beginner-friendly terms, especially for developers coming from another language or stack. Use when the user wants a walkthrough of runtime flow, file responsibilities, concrete examples, or language-to-language mental mapping such as Go to JavaScript.
---

# Explain Code Flow

Use this skill when the user wants to understand existing code, a recent change, or a runtime path in plain language.

This skill is especially useful when:
- the user is new to the language or framework
- the user asks for "how this works" rather than just "what changed"
- the user wants a doc they can keep and reuse
- the user asks for comparisons to a language they already know, such as Go to JavaScript

## Goal

Produce an explanation that is grounded in the actual codebase, not a generic language tutorial.

The output should help the user answer:
- where execution starts
- which files matter
- how data moves through the system
- why the change was made
- what to watch for when maintaining it

## Workflow

1. Inspect the code before explaining.
   Read the primary entrypoint, the helper functions it calls, and the relevant tests.

2. Identify the smallest real execution path.
   Prefer one concrete path such as:
   - request enters service method
   - service reads evaluation/findings
   - helper decides flags
   - reasons are merged
   - result is persisted or returned

3. Explain file ownership.
   For each important file, state its role in one sentence.
   Example:
   - `service/qc.go`: orchestrates the compliance and escalation flow
   - `service/flags.go`: contains the reusable flag decision helpers
   - `service/flags_test.go`: proves the edge cases and expected outcomes

4. Translate syntax into the user's mental model.
   If the user is coming from JavaScript, compare:
   - struct fields to object properties
   - methods with pointer receivers to instance methods mutating shared state
   - `map[string]T` to plain objects or Maps
   - `if err != nil` to explicit error-return checks instead of exceptions
   - proto getters like `GetFlags()` to defensive property access

5. Prefer examples over abstractions.
   Use one or two real examples from the code, tests, fixtures, or provided payloads.
   Show how a specific input becomes a specific output.

6. Separate behavior from implementation detail.
   Explain:
   - what decision is being made
   - what signals feed that decision
   - where the logic lives
   - why it is written that way

7. Call out maintenance constraints.
   If logic is coupled to config, scorecards, metric keys, schemas, or upstream metadata, say so explicitly.

## Output Shape

Default to this structure when useful:

### 1. What Changed
Explain the behavior change in plain language.

### 2. Main Files
List the 2-5 key files and what each one does.

### 3. Runtime Flow
Walk through the call path step by step in execution order.

### 4. Data and Decisions
Explain the important inputs, helper functions, and decision points.

### 5. Language Translation
Map confusing language-specific syntax to the user's familiar language or framework.

### 6. Concrete Example
Use a real example and trace it through the code.

### 7. Maintenance Notes
Call out what will need to change if upstream business logic, config, or schemas change.

## Style Rules

- Be concrete and repository-specific.
- Do not dump large code blocks unless the user asks.
- Prefer short excerpts and paraphrased explanations.
- Use file references so the user can jump into the code.
- Avoid teaching the entire language when only a few constructs matter.
- If a test already captures the behavior well, use it as part of the explanation.
- If the user asks for a persistent artifact, write a markdown doc in the repo.

## When Creating a Repo Doc

If the user wants a written explainer in the repository:
- create a focused markdown file under `docs/` unless the repo uses a different docs location
- keep it centered on the actual code path, not broad theory
- include examples from the current implementation
- mention tradeoffs and maintenance boundaries

## Example Prompt Patterns

This skill fits requests like:
- "Explain how this flow works, I am new to Go"
- "Make a doc so I can understand this change later"
- "Walk me through the code path like I am a JavaScript developer"
- "Show me how the request moves through these files"
- "Turn this implementation into a beginner-friendly explainer"

## Good Outcome

A good result leaves the user able to:
- follow the code path without guessing
- understand why the change exists
- connect the source language syntax to concepts they already know
- safely modify the implementation later
