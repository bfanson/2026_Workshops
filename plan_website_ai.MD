# 2026 Workshops Website — Design & Build Reference

This document records the decisions behind this Quarto website, as requested in the original
`plan_webpage.MD` brief (in the `2026_analysisAI` project). The scaffold described here has
already been built; use this as a reference when filling in real content or making future changes.

## 1. Tooling decision: Quarto vs. blogdown

**Chose Quarto.** It has native support for everything the brief needed: a navbar with a
dropdown menu (`website.navbar.right` with a `menu:`), and a blog-style "listing" page type for
the Home page that renders a title in large font with a description underneath — no custom
templating required. blogdown would have required managing a Hugo theme to get the same result,
for no real benefit here.

## 2. Site architecture

```
Navbar (left):  Home | Module 1 | Module 2 | Module 3 | Module 4
Navbar (right): Workshop Links ▾
                  ├── Agenda
                  └── Posit AI
```

| File | Title | Description |
|---|---|---|
| `index.qmd` | 2026 Workshops | Blog-style listing linking to Modules 1–4 |
| `agenda.qmd` | Agenda | Workshop schedule |
| `posit-ai.qmd` | Posit AI | Overview of Posit AI |
| `module1.qmd` | Module 1 | AI and data science |
| `module2.qmd` | Module 2 | On to QGIS |
| `module3.qmd` | Module 3 | Vibe-coding |
| `module4.qmd` | Module 4 | e-DNA!!! |

The Home page (`index.qmd`) uses a Quarto `listing` (`type: default`, `fields: [title,
description]`, `sort: false`) over the four module pages, which is why each module's `description:`
front matter matters — it's what shows up as the blurb under the title on Home.

## 3. Theme: matched to `2025_Models_workshop`

An initial version of this theme was guessed from Victorian Government / `ari.vic.gov.au`
branding, but was replaced after feedback to instead match the actual look of
`2025_Models_workshop` — a prior distill-based workshop site — by reading its hand-authored
`theme.css` directly rather than guessing.

Colours and base typography are defined in `_brand.yml` (Quarto's brand file, picked up
automatically); a few rules distill supported that `_brand.yml` doesn't have an equivalent for
(per-component fonts, link underline style, list marker colour) live in `custom.css`, loaded via
`format.html.css` in `_quarto.yml`.

**Palette (from `2025_Models_workshop/theme.css`):**

| Role | Hex | Used for |
|---|---|---|
| Indigo (primary) | `#201647` | Navbar background, links, list markers |
| Teal (secondary) | `#0F2E3D` | Footer background |
| Salmon | `rgba(249, 83, 85, 0.1)` | Inline code background highlight |

**Typography (from the same file):** body/headings in **Lato**, navbar specifically in
**Montserrat** (semi-bold), code in **Fira Mono** — all Google Fonts, matching the original
exactly (no substitution needed this time, unlike the earlier Brand Victoria attempt which used a
proprietary font).

If the look needs adjusting further, edit `_brand.yml` (colours/base fonts) or `custom.css`
(component-specific rules) and re-render.

## 4. Project structure

```
2026_Workshops/
├── 2026_Workshops.Rproj
├── _quarto.yml          # project/navbar config, output-dir: docs
├── _brand.yml            # colour palette & base typography, matched to 2025_Models_workshop
├── custom.css             # supplementary component-level styling (§3)
├── index.qmd              # Home — listing of modules
├── agenda.qmd
├── posit-ai.qmd
├── module1.qmd
├── module2.qmd
├── module3.qmd
├── module4.qmd
├── .gitignore             # ignores .quarto/ only
├── docs/                  # rendered output incl. docs/.nojekyll — served by GitHub Pages
└── plan_website_ai.MD     # this file
```

`output-dir: docs` in `_quarto.yml` means `quarto render` writes the built site straight into
`docs/`, which is what GitHub Pages will serve (§5). `docs/.nojekyll` was added by hand — plain
`quarto render` does not create it automatically (only `quarto publish` does); it's still good
practice to keep it so GitHub's Jekyll processing never interferes with generated `_`-prefixed
folders in the output.

## 5. Deployment to GitHub Pages

Local repo is initialised (`git init`, first commit on branch `main`). To finish deployment:

**One-time setup:**
1. On github.com, create a new empty repository named `2026_Workshops` under the `bfanson`
   account (no README/license).
2. From this project folder:
   ```
   git remote add origin https://github.com/bfanson/2026_Workshops.git
   git push -u origin main
   ```
3. On GitHub: **Settings → Pages** → Source: "Deploy from a branch" → Branch: `main`, Folder:
   `/docs` → **Save**.
4. After a minute or two, the site is live at `https://bfanson.github.io/2026_Workshops/`.

**Every time content changes:**
```
quarto render
git add .
git commit -m "Update site content"
git push
```

This mirrors how `2025_Models_workshop` (the site whose look originally inspired the Home page
listing layout) is deployed.

## 6. Content to-do

Everything below is currently a placeholder (`*Placeholder — ... will be added here.*`) and
needs real content before this goes live:

- [ ] **Agenda** — actual schedule: dates, times, sessions, facilitators.
- [ ] **Posit AI** — overview content. `2026_analysisAI/thoughts.MD` and
  `assessment_of_my_workflow.MD` may be useful source material.
- [ ] **Module 1 — AI and data science** — same two files above are plausible starting points.
- [ ] **Module 2 — On to QGIS** — no prior workshop material to draw on; starting from scratch.
  (Title assumes "Onto to QGIS" in the original brief was a typo for "On to QGIS" — revisit if not.)
- [ ] **Module 3 — Vibe-coding** — starting from scratch.
- [ ] **Module 4 — e-DNA!!!** — starting from scratch.
- [ ] Decide whether module entries should show a date/facilitator on the Home page listing (the
  `2025_Models_workshop` "Daily Sessions" layout this was modeled on included one) — currently
  omitted since modules don't have that metadata yet.
