# Generating Slides

The decks use the bundled MLH theme (`slides/mlh.css`). Compile with:

```bash
npx -y @marp-team/marp-cli slides/<deck>.md \
  --theme-set slides/mlh.css --output slides/<deck>.html
```

The VS Code Marp extension works too (point `marp.themeSet` at `slides/mlh.css`).
