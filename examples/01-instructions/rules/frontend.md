# Path-scoped rules (`rules/frontend.md`)

## What this file is

Sometimes rules only apply to part of the repo. Instead of one giant `AGENTS.md`,
you can keep **scoped** rule files and attach them to a path. This keeps context
small and relevant.

## Generic (vendor-neutral)

```markdown
# Rules for <path glob>

- <rule>
- <rule>
```

Attach it to a path in whatever your tool uses for instruction sources.

## opencode-specific

List scoped instruction files alongside the main one; the assistant applies them
when working in the matching area:

```jsonc
{ "instructions": ["AGENTS.md", "examples/01-instructions/rules/frontend.md"] }
```

---

## Example: rules/frontend.md (copy this)

```markdown
# Rules for frontend/**

- React function components only; no class components.
- Co-locate styles with the component.
- Use the API client in `src/api/client.ts` — never `fetch` directly.
- Keep components under 150 lines; extract when they grow.
- Accessibility: every interactive element needs an accessible name.
```
