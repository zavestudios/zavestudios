# Mermaid version override

`mermaid.min.js` here is a deliberate override of the copy bundled by the
theme. Hugo unions this project's `assets/` over a module's, so this file wins
over `hugo-theme-relearn`'s and is what the site actually loads, locally and in
CI alike.

| | |
|---|---|
| Pinned version | 11.17.2 |
| Theme ships | 11.8.0 |
| Added | 2026-09-05 |

## Why

Mermaid 11.8.0 has no `person` shape. Flowchart gained eight shapes after it —
`person`, `datastore`, `bucket`, `cloud`, `browser`, `console`, `folder`,
`bang` — and `docs/architecture/` uses them to draw C4-style diagrams without
dropping to the `C4Context` diagram type, which would cost the layout control
those pages need.

Relearn's `main` still shipped 11.8.0 when this was added, so bumping the theme
was not an alternative.

## Remove this when

Relearn ships Mermaid 11.17.2 or newer. Then delete this whole directory and
let the theme's copy take over again.

Left in place indefinitely, this pins the site to an aging Mermaid and the
failure is silent: the theme updates, nothing changes, and nobody knows why.
Check with:

```
curl -sL https://raw.githubusercontent.com/McShelby/hugo-theme-relearn/main/assets/js/mermaid/mermaid.min.js \
  | grep -o -m1 -E 'version:"[0-9]+\.[0-9]+\.[0-9]+"'
```

## Compatibility

Relearn's `theme.js` calls only `mermaid.initialize`, `mermaid.run`,
`mermaid.mermaidAPI`, and `mermaid.mermaid`. All four exist in 11.17.2, and the
bump stays inside major version 11. Rendering is client-side, so `docs-quality`
link checking will not catch a broken diagram — verify in a browser, in both
light and dark themes, after changing this file.
