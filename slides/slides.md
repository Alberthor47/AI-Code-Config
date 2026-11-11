---
marp: true
theme: mlh
paginate: true
size: 16:9
title: "AI Coding Assistant Best Practices"
footer: "AI Coding Assistant Best Practices | Major League Hacking"
---

<!-- _class: lead -->
<!-- _footer: '' -->

# AI Coding Assistant Best Practices

**Facilitator:** Alberto Camarena
**Presented at:** Global Hack Week

<!-- Speaker Notes: Welcome, greet chat. One line: "By the end you'll have an
accurate mental model of what an AI coding assistant actually is, and habits to
use it well this weekend." Pin the repo link in chat. -->

---

# Workshop Agenda

- **Module 0** — Why this matters
- **Module 1** — Anatomy of an AI coding assistant
- **Module 2** — The LLM layer
- **Module 3** — Context, memory & sessions
- **Module 4** — Tools & the harness
- **Module 5–6** — Skills, agents & subagents
- **Module 7** — MCP & data sources
- **Module 8** — Best practices
- **Module 9** — Inspect the configs

<!-- Speaker Notes: Flyover. No install today — it's a mental-model session with
config files to inspect. Questions welcome in chat. -->

---

# What You'll Walk Away With

By the end you can:

1. Explain the **parts** of an AI coding assistant (LLM, harness, tools, context).
2. Choose a **model and provider** deliberately, not by default.
3. Use **context, memory files, and sessions** on purpose.
4. Know what **skills, agents, and MCP** are and when to reach for them.
5. Apply a safe **plan → build → verify** workflow.

<!-- Speaker Notes: Set expectations: this is the "how to think about it" session
that makes the rest of GHW easier. -->

---

<!-- _class: divider -->

# Module 0 — Why This Matters

<!-- Speaker Notes: Transition into the framing. -->

---

# The "Should I Use AI?" Debate Is Over

- In 2023 this was a real argument. In 2026 it's table stakes.
- Every serious team ships with an AI assistant in the loop.
- The useful question is no longer _if_, it's **how**.
- Using it badly is now the real risk: shipped bugs, leaked secrets,
  code you can't explain.

<!-- Speaker Notes: Be direct: "I'm not going to relitigate whether you should use
AI — that ship sailed. I care that you use it well this weekend." -->

---

# The Real Question: How to Use It _Well_

- An AI assistant is a **power tool, not an oracle**.
- Its output is a **draft you own** — you review, verify, and commit.
- The skill isn't prompt tricks; it's **context, verification, and judgment**.

<!-- Speaker Notes: Land the thesis. Everything else is habits that follow from it. -->

---

# Autocomplete → Chat → Agent

| Mode             | You get                         | You still do           |
| ---------------- | ------------------------------- | ---------------------- |
| **Autocomplete** | next-line suggestions           | everything else        |
| **Chat**         | answers, snippets, explanations | paste + adapt          |
| **Agent**        | multi-step edits across files   | direct, review, verify |

The jump to **agent** is what changed everything — and what this session is about.

<!-- Speaker Notes: Most people have used the first two. The agent reads files,
runs commands, edits code — power that requires the rest of this session. -->

---

# The Pair-Programmer Mental Model

Treat it like a **fast, tireless pair** — not a senior engineer:

- **You drive.** It proposes; you own the code.
- **Explain intent** before asking for code.
- **Review every diff** like a teammate's PR.
- **Keep it focused** — small tasks, small diffs.

**Same tool, two outcomes:** good = clear goal + right files + reviewed diffs;
bad = paste error, accept everything, commit → tangled code you can't explain at
judging time.

<!-- Speaker Notes: This frame recurs all session. You wouldn't merge a stranger's
PR unreviewed; don't do that here. -->

---

<!-- _class: divider -->

# Module 1 — Anatomy of an AI Coding Assistant

<!-- Speaker Notes: "Open the hood. Name the parts, then everything else is
zooming in." -->

---

# The Anatomy (the one diagram)

```
        ┌─────────────────────── YOUR REPO / ENVIRONMENT ───────────────────────┐
        │                                                                       │
        │   ┌──────────────────────────  HARNESS  ──────────────────────────┐   │
        │   │  context assembly ─▶ ┌─────────┐ ─▶ tool loop ─▶ permissions  │   │
        │   │  instructions/rules ▶│   LLM   │ ◀─ tools (read/edit/bash)    │   │
        │   └──────────────────────└─────────┘──────────────────────────────┘   │
        │             ▲                                            │            │
        │      context sources                          extensions │            │
        │   (files, git, docs, MCP)                    (skills, agents, MCP)    │
        └───────────────────────────────────────────────────────────────────────┘
```

The **LLM** is just the brain. The **harness** around it is what makes it a usable coder.

<!-- Speaker Notes: The anchor diagram. Every module zooms into one box. If they
remember one picture, it's this. -->

---

# The LLM and the Harness

- **LLM** — predicts text; it does **not** know your repo, only what it's handed.
- **Harness** — the program around the model. It:
  - assembles **context** each turn (system prompt + rules + files + history),
  - exposes **tools** and runs the **agent loop**,
  - enforces **permissions** and sandboxing,
  - manages **sessions** and memory.

Swap the model → behavior changes. Swap the harness → _workflow_ changes.

<!-- Speaker Notes: This is why the same model feels different in different tools:
different harnesses. -->

---

# Tools (the hands)

Actions the model can request:

- **read / search** — open files, grep, glob
- **edit** — apply a diff
- **run** — shell command, tests
- **fetch** — call an API or MCP server

The model picks tools; the harness executes and feeds results back. No tools, no agent.

<!-- Speaker Notes: "Tools are function calling." The model emits a structured
call; the harness runs it. -->

---

# Where It Runs (the environment)

The same core shows up in different surfaces:

- **Terminal / CLI** — full repo access, scriptable, high autonomy
- **Desktop apps** — a GUI over the harness, previews
- **IDE / editor plugins** — inline, cursor-aware, tight loop

Pick the surface for the task: big refactor → CLI; tweak a function → inline plugin.

<!-- Speaker Notes: Ask chat which one they use. Concepts identical; surface
changes context access and autonomy. -->

---

<!-- _class: divider -->

# Module 2 — The LLM Layer

<!-- Speaker Notes: "The brain itself: what it is, what limits it, how you choose." -->

---

# What an LLM Is — and What Limits It

- Predicts the **next token**, extremely well.
- **No memory between sessions** unless the harness gives it one.
- Quality = training × **context** × how you steer it.

**Tokens:** ~4 chars each. The **context window** is the max tokens it can
consider at once (input + output). Everything costs tokens — your prompt, rules,
every file, every tool result. Bigger window ≠ free: money, latency, attention.

<!-- Speaker Notes: "Context window = working memory." When it fills, older stuff
is dropped or summarized — which is why sessions and memory matter next. -->

---

# Model Families & Choosing One

- **Reasoning models** — slower, deliberate; hard/ambiguous problems.
- **Fast models** — quick, cheap; edits and routine work.
- **Multimodal** — accept images/diagrams.
- **Tiers** — mini / standard / frontier: cost ↔ latency ↔ capability.

Checklist: **capability · cost · latency · privacy · reliability.**
Don't use the frontier model to rename a variable.

<!-- Speaker Notes: Many tools use a cheap "small model" for titles/summaries and a
big one for the work. Default to fast for editing, reasoning for hard design. -->

---

# Providers: Where the Model Comes From

- **First-party** — Anthropic, OpenAI, Google (direct APIs).
- **Routers / aggregators** — e.g. **OpenRouter**: one key, many models.
- **Local** — e.g. Ollama: private, limited.

```jsonc
{ "provider": "<name>", "model": "<vendor>/<model-id>", "apiKey": "$ENV_VAR" }
```

Keep keys in **env vars**, never in a committed file.

<!-- Speaker Notes: Providers are plumbing; the interesting choice is the model.
We'll see a real opencode config in Module 9. -->

---

# Routers in Practice (e.g. OpenRouter)

Why people use a router:

- **One key, many models** — try Gemini/Claude/GPT without N accounts.
- **Automatic fallback** if a provider is down or rate-limited.
- **Usage/price visibility** across models.
- Trade-off: an extra hop, and **your data goes through the router**.

<!-- Speaker Notes: MLH-style events hand out one OpenRouter key so people can
experiment. Flag the data-policy question for sensitive projects. -->

---

<!-- _class: divider -->

# Module 3 — Context, Memory & Sessions

<!-- Speaker Notes: "The most important module. If you change one habit today,
make it this." -->

---

# The Model Is Stateless

- Between calls, the model remembers **nothing**.
- The harness rebuilds the context **every turn**.
- So "why did it forget?" almost always means "it wasn't in the context (anymore)."

<!-- Speaker Notes: Reframes a lot of confusion: "it forgot my instruction" = that
instruction fell out of the window. -->

---

# What's in the Window Each Turn

```
 system prompt + rules ─┐
 your instructions      ├─▶  [ context window ]  ─▶  model
 files it opened        │
 conversation history   │
 tool outputs  ─────────┘
```

Lead with intent, then the **relevant** files, then the error. Dumping 12 files buries the signal.

<!-- Speaker Notes: Order and size matter. What's near the top and what's large
shapes the answer. -->

---

# Sessions, History & Memory

- A **session** (thread) is one ongoing conversation with state.
- **Short-term memory** = the context window (this session).
- **Long-term memory** = files reloaded every time: rules, memory files, docs.
- History accumulates — good for continuity, bad for cost and "context rot". Start a **fresh session** for a new task.

<!-- Speaker Notes: "If it's getting weird, start a new session and restate the
goal." Cheap and effective. You control both layers. -->

---

# Memory Files (`AGENTS.md` and friends)

A plain file in your repo the harness loads as **persistent instructions**:

- project overview & conventions
- how to run tests / build / lint
- do's and don'ts ("never touch `migrations/`")

Names vary by tool, the **concept is universal**. It's the highest-leverage 30
minutes of setup you can do.

<!-- Speaker Notes: We'll inspect a real AGENTS.md in Module 9. -->

---

# Compaction & "Context Rot"

- When the window fills, harnesses **compact**: summarize older turns.
- Compaction loses detail — subtle constraints quietly disappear.
- Symptoms: it "forgets" rules, repeats work, drifts off-goal.

Fix: shorter sessions, smaller context, restate key constraints, keep rules in a file.

<!-- Speaker Notes: Signal: quality drops mid-session. Often a full window, not a
dumber model. -->

---

# Anti-Patterns (don't do these)

- Pasting huge files "just in case".
- No rules/memory file → repeat context every session.
- One mega-session for the whole project.
- Stale context — files changed, the session didn't.
- Trusting a summary of code it didn't re-read.

<!-- Speaker Notes: Each has a cheap fix from the previous slides. -->

---

<!-- _class: dark -->

# Magic Moment

The **same prompt**, two sessions:

- A: no rules, no files, vague ask → meh.
- B: `AGENTS.md` + the 2 relevant files + a clear goal → **it just works**.

Context beats cleverness.

<!-- Speaker Notes: If you demo one thing, demo this: same model, same prompt,
better context, dramatically better result. -->

---

<!-- _class: divider -->

# Module 4 — Tools & the Harness

<!-- Speaker Notes: "How the harness acts, and how you keep it safe." -->

---

# Tool Calling & the Agent Loop

1. You ask.
2. The model returns a **structured request** to use a tool (e.g. read `src/app.ts`).
3. The harness **runs** it and appends the result to context.
4. Repeat until it answers.

```
ask ─▶ think ─▶ call tool ─▶ observe ─▶ think ─▶ ... ─▶ answer
```

You can **interrupt, steer, and review** between steps. Watch the loop; don't walk away from an unattended agent on your main branch.

<!-- Speaker Notes: This loop is the whole difference between chat and an agent. -->

---

# Permissions: allow / ask / deny

- **allow** — safe, read-only (read, grep, status)
- **ask** — anything that changes state or reaches the network
- **deny** — destructive/dangerous (`sudo`, `rm -rf`, `push`)

```jsonc
{ "permission": { "bash": { "*": "ask", "git push*": "deny" } } }
```

Edits arrive as **diffs** you review; commands often run in a **sandbox**.
Prefer safe-by-default, grant narrowly. A diff you didn't read is a bug you shipped.

<!-- Speaker Notes: This is the seatbelt. Review isn't optional — it's the job. -->

---

# Plan vs Build

- **Plan mode** — explores and proposes, changes nothing.
- **Build mode** — edits and runs commands.

Plan first for anything non-trivial; approve the plan; then build.

<!-- Speaker Notes: Catches bad approaches before they cost you an afternoon. -->

---

# Safety & Secrets

- Never commit keys — `.env` (gitignored) + `.env.example`.
- Be careful with `curl | sh`, new packages, and unknown scripts.
- Treat agent claims about **security** as untrusted until verified.
- Don't point an agent at a repo full of secrets.

<!-- Speaker Notes: Supply chain + secrets are the two real footguns. -->

---

<!-- _class: divider -->

# Module 5 — Skills

<!-- Speaker Notes: "First extension: packaged knowledge." -->

---

# What a Skill Is

**Packaged procedural knowledge** the assistant loads **when relevant**:

- a `SKILL.md` with `name` + `description` (how it's discovered)
- instructions, and optionally scripts/reference files
- loaded on demand — so it doesn't bloat every session

Think: **a recipe it can follow**, not a prompt you retype.
If you've pasted the same instructions twice — make it a skill.

<!-- Speaker Notes: Contrast with memory files: rules are always-on; skills load
on demand. Rule of thumb: third time you paste it, package it. -->

---

# Anatomy of a `SKILL.md`

```markdown
---
name: my-skill
description: What it does AND when to trigger it (keywords).
---

# My Skill

## Steps

1. ...

## Guardrails

- ...
```

The **description** is the trigger — write it for discovery, not for humans.

<!-- Speaker Notes: We'll see a real one in Module 9. The description is
essentially the API — it decides when the skill activates. -->

---

<!-- _class: divider -->

# Module 6 — Agents & Subagents

<!-- Speaker Notes: "Second extension: workers." -->

---

# Agent = Model + Tools + Loop

An **agent** is a model with tools and a loop, given a goal. A **subagent** is a
second agent the main one delegates to — narrow job, its own context.

**Good fit:** multi-step, multi-file, clear success criteria (refactor, migrate, add tests).
**Bad fit:** vague goals, one-line edits, anything you can't verify.

Multi-agent is **not** automatically better — more parts, more to debug.

<!-- Speaker Notes: Delegation keeps the main context clean. Don't reach for a
fancier setup than the task needs. -->

---

<!-- _class: divider -->

# Module 7 — MCP & Data Sources

<!-- Speaker Notes: "Third extension: connecting the outside world." -->

---

# Your Assistant Doesn't Know Your Stuff

- It can't see your database, docs, tickets, that API.
- Pasting data is manual, lossy, and stale.
- We want it to **reach out and ask** for what it needs.

**MCP (Model Context Protocol)** — an open standard for connecting assistants to
tools and data: **servers** expose capabilities (query a DB, browse a page, read
docs); **clients** in your harness call them. Pluggable, like USB-C for AI tools.

<!-- Speaker Notes: The point: instead of N bespoke integrations, models talk one
protocol and servers plug in. -->

---

# Grounding: Repo vs External

- **Repo grounding** — read/search your codebase (built in).
- **External grounding** — via MCP: databases, browser, docs, tickets.
- Examples: a MongoDB MCP server, a Playwright browser MCP, a docs-knowledge MCP.

```jsonc
// generic            { "mcp": { "<name>": { "command"|"url": "..." } } }
// opencode           { "mcp": { "playwright": { "type": "local",
//                      "command": ["npx","-y","@playwright/mcp@latest"] } } }
```

<!-- Speaker Notes: Give it the right data source instead of pasting by hand. See
examples/03-mcp/mcp.json. -->

---

<!-- _class: divider -->

# Module 8 — Best Practices

<!-- Speaker Notes: "Everything comes together into habits." -->

---

# The Workflow: Plan → Build → Verify

1. **Plan** — describe the goal; ask for a plan; read it.
2. **Build** — small changes, one concern at a time.
3. **Verify** — run the tests/build yourself; read the diff; only then move on.

If a step is fuzzy, go back to the previous one. Never skip Verify.

<!-- Speaker Notes: The single most valuable slide in the deck. Say it slowly. -->

---

# Prompting for Coders

- State **intent and constraints**, not just the symptom.
- Point it at the **relevant files**, not the whole repo.
- Ask for a **plan first** on anything multi-step.
- Ask it to **explain the diff** — that's how you learn and catch errors.

<!-- Speaker Notes: "Brief it like a pair." Intent + constraints + relevant files. -->

---

# Review Every Diff — and Recover

- Read the diff as if a stranger wrote it (effectively, one did).
- Small, focused changes are easy to review and revert; tests turn "looks right" into "is right".
- **Failure modes → fixes:**
  - wrong file / stale context → re-point it
  - looping → interrupt, restate, smaller step
  - breaks the build → revert, narrow the change
  - confidently wrong API → verify against docs

<!-- Speaker Notes: Recovery is a skill. Small steps mean small recoveries. -->

---

# Security & When _Not_ to Use the Agent

- Never paste secrets; keep them in `.env` (gitignored).
- Don't run piped shell scripts from the agent blindly.
- **Skip the agent** for: credential/token handling, destructive ops you can't
  undo, and anything you can't verify.

<!-- Speaker Notes: The agent is a tool, not a scapegoat — you own what you commit.
Judging will ask you to explain your code. -->

---

<!-- _class: divider -->

# Module 9 — Inspect the Configs

<!-- Speaker Notes: "Open the example files. Generic idea first, then the concrete
opencode version." -->

---

# Tour: The `examples/` Folder

```
examples/
├── 01-instructions/AGENTS.md         # persistent rules
├── 01-instructions/rules/frontend.md # path-scoped rules
├── 02-config/opencode.jsonc          # agents, permissions, mcp
├── 02-config/providers.openrouter.jsonc
├── 03-mcp/mcp.json                   # an MCP server
├── 04-skills/example-skill/SKILL.md  # a skill
└── 05-agents/reviewer.agent.md       # a subagent
```

Each file: a **generic** version first, then the **concrete opencode** variant at
the bottom. `AGENTS.md` = standing orders · `opencode.jsonc` = models/agents/permissions/MCP.

<!-- Speaker Notes: Walk the folder live; open each file; call out the one thing
to notice. Encourage copying the opencode variant. -->

---

<!-- _class: divider -->

# Module 10 — Recap

<!-- Speaker Notes: "Let's land it." -->

---

# Your Checklist

- [ ] Use it as a **pair**, not an oracle — review every diff.
- [ ] Give **context**: a rules file + the right files, not the whole repo.
- [ ] Pick the **model** for the task; keep keys in env vars.
- [ ] Keep **sessions** short; restart when it drifts.
- [ ] **Plan → build → verify**; small diffs; run the tests.
- [ ] Reach for **skills / agents / MCP** only when the task needs them.

<!-- Speaker Notes: This checklist also lives in the README. Screenshot it. -->

---

# Resources

- Your provider's model catalog & pricing (e.g. OpenRouter's model list).
- Your tool's docs for **rules (`AGENTS.md`), skills, agents, and MCP**.
- The Model Context Protocol spec (for your own data).
- The `examples/` folder in this repo — copy and adapt.

<!-- Speaker Notes: Point at the repo; remind them the examples are copy-pasteable. -->

---

<!-- _class: lead -->
<!-- _footer: '' -->

# Thank You! Questions?

**Facilitator:** Alberto Camarena
**Presented at:** Global Hack Week

<!-- Speaker Notes: Thank them, take questions, remind them this is pre-work for
the weekend. Go build something. -->
