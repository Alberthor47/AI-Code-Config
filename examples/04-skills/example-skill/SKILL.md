---
name: conventional-commits
description: >-
  Create well-formed Conventional Commits for the current repo. Use when
  committing, when the user says "commit this", "write a commit message", or
  when staging changes and a message is needed.
---

# Conventional Commits

## What this file is

A **skill**: packaged procedural knowledge the assistant loads **when relevant**.
The `description` frontmatter is the trigger — it's what decides when this skill
activates, so write it for discovery (include the words a user would say).

## When to use

- The user asks to commit and the repo uses Conventional Commits.
- You need a concise, scoped commit message for a staged change.

## Steps

1. Inspect what changed:
   ```bash
   git status --short && git diff --staged --stat
   ```
2. Choose a type: `feat`, `fix`, `refactor`, `docs`, `test`, `chore`, `perf`, `build`, `ci`.
3. Write: `type(scope): imperative summary` (≤ 72 chars).
4. Add a body only if the **why** isn't obvious; wrap at 72 chars.
5. Never commit unless the user asked. One logical change per commit.

## Guardrails

- Do not include secrets or `.env` files.
- Do not amend or force-push unless explicitly asked.
- Match the repo's existing style if it differs.

---

<!--
Placement (opencode-specific):
- Project: `./.opencode/skills/conventional-commits/SKILL.md`
- Global:  `~/.config/opencode/skills/conventional-commits/SKILL.md`
The folder name must match the `name` field. Skills load with progressive
disclosure: only the name + description are always visible; the body loads on use.
-->
