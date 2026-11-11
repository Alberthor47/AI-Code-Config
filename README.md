# 🧠 AI Coding Assistant Best Practices

> **MLH Global Hack Week Workshop** — pre-work session
> **Facilitator:** Alberto Camarena

A 90-minute, no-code pre-work session on how to use an AI coding assistant well.
You'll leave with an accurate **mental model** of what these tools are, and a set
of **habits** for using them safely and effectively this weekend.

---

## 🎯 Learning Objectives

By the end of this session, participants will be able to:

1. Explain the **parts** of an AI coding assistant — LLM, harness, tools, context.
2. Choose a **model and provider** deliberately (including routers like OpenRouter).
3. Use **context, memory files, and sessions** on purpose.
4. Describe what **skills, agents/subagents, and MCP** are and when to reach for them.
5. Apply a safe **plan → build → verify** workflow, including failure recovery.

---

## 📂 Repository Structure

```
.
├── slides/
│   ├── slides.md            # Marp source (with speaker notes)
│   ├── slides.html          # Compiled standalone deck
│   ├── mlh.css              # MLH Marp theme
│   ├── assets/mlh-logo-*.svg
│   └── readme.md            # How to compile the slides
├── examples/                # Config files to inspect (generic + opencode-specific)
│   ├── 01-instructions/
│   ├── 02-config/
│   ├── 03-mcp/
│   ├── 04-skills/
│   └── 05-agents/
├── .gitignore
└── LICENSE
```

---

## 🚀 Quick Start — Viewing the Slides

Open `slides/slides.html` in any browser, or recompile after editing:

```bash
npx -y @marp-team/marp-cli slides/slides.md \
  --theme-set slides/mlh.css --output slides/slides.html
```

(Pass the deck filename **before** `--theme-set` — it's an array option.)

---

## 🔍 The `examples/` Folder

Every file has a **generic (vendor-neutral)** version and a concrete
**opencode** variant you can copy. See [`examples/README.md`](examples/README.md).

| File                                   | Concept                                          |
| -------------------------------------- | ------------------------------------------------ |
| `01-instructions/AGENTS.md`            | Persistent rules loaded every session            |
| `01-instructions/rules/frontend.md`    | Path-scoped rules                                |
| `02-config/opencode.jsonc`             | Harness config: models, agents, permissions, MCP |
| `02-config/providers.openrouter.jsonc` | One key, many models                             |
| `03-mcp/mcp.json`                      | Connecting external tools & data                 |
| `04-skills/example-skill/SKILL.md`     | A packaged procedure                             |
| `05-agents/reviewer.agent.md`          | A focused reviewer subagent                      |

> Model IDs, package names, and config keys change — confirm them against current
> docs before relying on an example verbatim.

---

## ✅ Quick Checklist

- Use it as a **pair**, not an oracle — review every diff.
- Give **context**: a rules file plus the right files, not the whole repo.
- Pick the **model** for the task; keep keys in env vars.
- Keep **sessions** short; restart when it drifts.
- **Plan → build → verify**; small diffs; run the tests.
- Reach for **skills / agents / MCP** only when the task needs them.

---

## 📚 Resources

- Your provider's model catalog & pricing (e.g. OpenRouter's model list).
- Your tool's docs for **rules (`AGENTS.md`), skills, agents, and MCP**.
- The Model Context Protocol spec (for connecting your own data).
- The `examples/` folder in this repo — copy and adapt.

---

## 📄 License

MIT License. Created for MLH Global Hack Week.
