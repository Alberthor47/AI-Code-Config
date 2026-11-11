# Examples — AI coding assistant configs

Inspect and copy these. Every file has two parts:

1. **Generic** — the vendor-neutral idea.
2. **opencode-specific** — a concrete, copy-pasteable variant (the tool used in
   this session).

| File                                   | Maps to module             | What to notice                                |
| -------------------------------------- | -------------------------- | --------------------------------------------- |
| `01-instructions/AGENTS.md`            | Context, memory & sessions | Standing orders loaded every session          |
| `01-instructions/rules/frontend.md`    | Context, memory & sessions | Path-scoped rules                             |
| `02-config/opencode.jsonc`             | Harness / tools            | Models, agents, permissions, MCP in one place |
| `02-config/providers.openrouter.jsonc` | LLM layer                  | One key, many models                          |
| `03-mcp/mcp.json`                      | MCP & data sources         | Plugging in external tools/data               |
| `04-skills/example-skill/SKILL.md`     | Skills                     | A packaged procedure                          |
| `05-agents/reviewer.agent.md`          | Agents & subagents         | A focused reviewer subagent                   |

> Model IDs and provider package names change often — always confirm them
> against the current docs before relying on an example verbatim.
