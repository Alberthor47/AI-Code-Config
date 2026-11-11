# `reviewer.agent.md` — a focused subagent

## What this file is

A **subagent**: a second agent the main one delegates to, with a narrow job and
its own context. Delegation keeps the main conversation clean — the subagent does
one task and reports back a summary.

## Generic (vendor-neutral)

```markdown
---
description: <what it does + when to delegate to it>
mode: subagent
model: <optional: cheaper/faster model>
permission:
  edit: deny            # a reviewer should not edit
---

<system prompt: role, focus, output format>
```

## opencode-specific — `.opencode/agent/reviewer.md` (copy this)

<!--
opencode discovers agent files at:
  project:  .opencode/agent/<name>.md
  global:   ~/.config/opencode/agent/<name>.md
The frontmatter configures it; the body becomes its prompt.
-->

```markdown
---
description: >-
  Reviews a diff or file for bugs, edge cases, security issues, and style.
  Use when the user asks to review code or before committing.
mode: subagent
model: openrouter/<vendor>/<model-id>
permission:
  edit: deny
  bash: ask
---

You are a strict but constructive code reviewer.

Focus, in order:

1. Correctness and edge cases.
2. Security (secrets, injection, unsafe input).
3. Performance and obvious inefficiencies.
4. Readability and conventions from AGENTS.md.

Output:

- A short verdict (approve / request changes).
- A bullet list of findings, each with file:line and a suggested fix.
- Do not rewrite the code unless asked; do not edit files.
```

## Why a subagent (not just a prompt)

- **Isolated context** — the review doesn't pollute your main session.
- **Least privilege** — `edit: deny` means it can't change your code.
- **Right model** — a fast model is often enough for a review pass.
