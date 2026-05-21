# mijelDevNotes — Agent Guide

**Docsify v4 static site** · Spanish docs · No build step · Deployed on Vercel

## Content

- All Markdown lives under `docs/`
- Navigation is managed in `docs/_sidebar.md` — must update it when adding/renaming pages
- The site is client-side rendered (no build, no server). Edit Markdown, refresh browser.
- Mermaid diagrams use `dark` theme. Tabs use `classic` theme with persistence.
- Syntax highlighting available: JS, TS, Python, Java, C#, Go, Rust (via Prism.js `index.html:86-92`)
- Search is built-in (Docsify search plugin, placeholder: "Buscar")

## Commands

```json
npm test   # placeholder: "echo 'Error: no test specified' && exit 1"
```

No linter, typechecker, or formatter configured. No test framework.

## Deploy

- **Platform**: Vercel (static export, no framework)
- Config: `vercel.json` rewrites all routes to `index.html` (SPA routing)
- `.nojekyll` present (required for GitHub Pages with `_`-prefixed dirs)

## Structure

```
docs/
  _sidebar.md                 ← navigation tree (must keep in sync)
  backend/laravel/
  buenas-practicas/solid/     ← SOLID principles
  frontend/nextjs/            ← NextJS notes + Zustand tutorial
  gestion-proyectos/kanban/
  gestion-proyectos/scrum/
  lenguajes-programacion/typescript/
  Ejercicios/                 ← coding exercises (narcissistic number, credit card mask, bit counting)
```

Each section has a `README.md` as entrypoint, linked from `_sidebar.md`.

## Conventions

- Content is in **Spanish** — titles, sidebar labels, UI strings, content
- Page files: kebab-case, descriptive names (e.g., `01-estado-en-nextjs.md`)
- Exercises use snake_case filenames (e.g., `numero_narcisista.md`)
- When adding content: create the Markdown file → add entry to `docs/_sidebar.md` → verify in browser
