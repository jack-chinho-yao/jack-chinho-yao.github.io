# About Me — GitHub Pages Personal Site (Design)

**Date:** 2026-07-08
**Status:** Approved

## Goal

Build a single-page personal "About Me" site for a **DevSecOps engineer**, hosted on
GitHub Pages at `<username>.github.io` (user site, served at the root domain).
Terminal / hacker aesthetic, green accent. Built with a real Node toolchain
(Vite + Tailwind) and deployed via GitHub Actions — the repo itself doubles as a
small CI/CD showcase for job hunting.

The user builds it themselves; this spec + the follow-up plan are a guided,
step-by-step tutorial.

## Tech Stack

- **Vite** (vanilla template, not React) — dev server + static build
- **Tailwind CSS v4** via the official `@tailwindcss/vite` plugin — CSS-first
  config using `@theme` (no `tailwind.config.js`)
- **Google Fonts:** `JetBrains Mono` (headings / accent / prompts), `Inter` (body)
- Minimal vanilla JS (`src/main.js`) for the blinking-cursor effect
- No React, no other framework

## Project Structure

```
<username>.github.io/
├─ index.html                     # Vite entry, page markup
├─ src/
│  ├─ main.js                     # small JS (blinking cursor, etc.)
│  └─ style.css                   # Tailwind entry + @theme palette + custom
├─ public/                        # static assets (favicon, etc.)
├─ package.json
├─ vite.config.js                 # registers @tailwindcss/vite plugin
├─ .gitignore                     # node_modules, dist
└─ .github/workflows/deploy.yml   # CI/CD
```

## Color Palette

Defined once in `src/style.css` under `@theme`, exposed as Tailwind utilities
(e.g. `bg-background`, `text-accent-green`).

| Role         | Hex       | Usage                         |
|--------------|-----------|-------------------------------|
| Background   | `#111111` | Page background               |
| Surface      | `#1a1a1a` | Card / section background     |
| Border       | `#2a2a2a` | Borders, dividers             |
| Primary text | `#ffffff` | Headings                      |
| Body text    | `#cccccc` | Paragraphs, descriptions      |
| Muted text   | `#888888` | Labels, secondary info        |
| Accent green | `#00ffab` | Links, highlights, accent     |
| Accent cyan  | `#00e5ff` | Secondary accent, hover state |

## Design Direction

- Terminal / hacker aesthetic; `>_` prompt motifs, blinking cursor on hero
- Monospace (`JetBrains Mono`) for headings/accents, sans-serif (`Inter`) for body
- **Flat design: no gradients, no shadows**
- Clean, responsive, mobile-friendly
- Placeholder text like `[Your Name]` so the user fills in real info later

## Page Sections

1. **Hero** — name, title (DevSecOps Engineer), one-line tagline, blinking cursor
2. **About** — short self-introduction paragraph
3. **Skills** — tech stack grouped by category (Cloud, IaC, Security, CI/CD, …)
4. **Certifications** — list with status (earned / in progress)
5. **Projects** — card grid with placeholder links (to future GitHub Pages repos)
6. **Contact** — GitHub and LinkedIn links

## Deployment — GitHub Actions

- User site `<username>.github.io` ⇒ Vite `base` stays default `/`
  (project pages would need `base: '/<repo>/'`; not our case)
- **Repo:** must be named exactly `<username>.github.io`
- **GitHub setting:** Settings → Pages → Source = **GitHub Actions**
- **Workflow** (`.github/workflows/deploy.yml`), on push to `main`:
  1. checkout
  2. setup Node + `npm ci`
  3. `npm run build` → outputs `dist/`
  4. `actions/configure-pages`
  5. `actions/upload-pages-artifact` (path `dist`)
  6. `actions/deploy-pages`
- Permissions: `pages: write`, `id-token: write`; concurrency group so
  overlapping pushes don't clobber each other.

### Future DevSecOps extensions (out of scope for v1, noted for later)

- Pre-build checks: HTML validation, link checking, `npm audit`
- CodeQL / dependency-review workflow

## Non-Goals (YAGNI)

- No React / SPA framework
- No CMS, no blog engine
- No analytics, no backend
- No light theme (dark terminal only)

## Success Criteria

- `npm run dev` serves the site locally with Tailwind working
- `npm run build` produces a static `dist/`
- Pushing to `main` auto-deploys; site is live at `https://<username>.github.io`
- Page renders correctly on mobile and desktop
