# AGENTS.md — persistent instructions for your assistant

## What this file is

A plain markdown file in your repo that the assistant loads as **standing
orders** every session. It is the highest-leverage setup you can do: it stops you
repeating the same context, and it encodes your conventions once.

## Generic (vendor-neutral) — what to put in a rules file

```markdown
# Project: <name>

<one-line description>

## Stack

- <language / framework / versions>

## Commands

- install: <cmd>
- run: <cmd>
- test: <cmd>
- lint: <cmd>

## Conventions

- <style rules, patterns to follow>


## Rules / guardrails

- Never edit <sensitive path>.
- Always <required step>.
```

Keep it short and specific. Rules that are easy to follow get followed.

## opencode-specific

opencode loads `AGENTS.md` from the project root automatically. You can also
point at extra files explicitly in `opencode.jsonc`:

```jsonc
{ "instructions": ["AGENTS.md", "docs/style.md"] }
```

---

## Example: AGENTS.md (copy this)

```markdown
# Project: geomap-proposals

A full-stack app for citizen proposals on a map. Backend: NestJS + Mongoose.
Frontend: Vite + React.

## Commands

- install: `npm install` (root, npm workspaces)
- dev: `docker compose up`
- backend test: `cd backend && npm test`
- frontend build: `cd frontend && npm run build`

## Conventions

- TypeScript strict; no `any`.
- API responses go through `backend/src/common/response.util.ts`.
- Commits: Conventional Commits.

## Rules

- Never edit `propuestas-ciudadanas-para-mapa-ciclista.csv` (seed data).
- Do not commit `.env`; use `.env.example`.
- Ask before adding a new dependency.
```
