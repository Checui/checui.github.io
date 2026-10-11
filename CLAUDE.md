# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## About this site

This is a personal academic website for Che Cui, a pre-doctoral researcher in the IMAGINE group at École des Ponts ParisTech (previously MRes in AI/ML at Imperial College London). It is built on the [al-folio](https://github.com/alshedivat/al-folio) Jekyll theme, deployed automatically to GitHub Pages from the `main` branch (the `gh-pages` branch is auto-generated and must not be touched).

## Local development

**Recommended (Docker):**

```bash
docker compose pull
docker compose up
# Site available at http://localhost:8080
```

**Slim image:**

```bash
docker compose -f docker-compose-slim.yml up
```

**Legacy (requires Ruby + Bundler + Python):**

```bash
bundle install
pip install jupyter
bundle exec jekyll serve
# Site available at http://localhost:4000
```

**Build only (no server):**

```bash
bundle exec jekyll build
# Output goes to _site/
```

## Formatting & linting

Prettier (with `@shopify/prettier-plugin-liquid`) is the enforced code formatter — the `prettier.yml` CI workflow runs `npx prettier . --check` on every push/PR to `main` and **fails the check** if any file is unformatted (it also uploads an HTML diff artifact). Before committing, run:

```bash
npx prettier . --write   # config: .prettierrc (printWidth 150, es5 trailing commas); ignores in .prettierignore
```

Optional `pre-commit` hooks (`.pre-commit-config.yaml`) handle trailing whitespace, end-of-file, YAML validation, and large-file checks.

## Key content files to edit

| What to change                                      | Where                                                            |
| --------------------------------------------------- | ---------------------------------------------------------------- |
| Personal bio, profile photo, announcements settings | `_pages/about.md`                                                |
| Publications                                        | `_bibliography/papers.bib`                                       |
| CV data                                             | `assets/json/resume.json` (primary) or `_data/cv.yml` (fallback) |
| Social links                                        | `_data/socials.yml`                                              |
| GitHub repos/users shown on repositories page       | `_data/repositories.yml`                                         |
| News items                                          | `_news/` directory                                               |
| Blog posts                                          | `_posts/` directory                                              |
| Projects                                            | `_projects/` directory                                           |
| Site-wide config (URL, theme, features)             | `_config.yml`                                                    |

## Architecture overview

- **`_config.yml`** — central configuration for all site features, third-party library versions, Jekyll Scholar (bibliography), pagination, image processing, and feature toggles (darkmode, math, analytics, etc.)
- **`_pages/`** — Markdown files for each top-level page; front matter controls layout, permalink, and page-specific settings
- **`_layouts/`** — Liquid templates defining page structures; `bib.liquid` controls publication card rendering
- **`_includes/`** — Reusable Liquid snippets (header, footer, citation, figure, etc.)
- **`assets/json/resume.json`** — CV in JSON Resume format; parsed by `jekyll-get-json` at build time and rendered via `_includes/resume/` partials
- **`_bibliography/papers.bib`** — BibTeX file processed by `jekyll-scholar`; custom fields (`abbr`, `selected`, `pdf`, `code`, `arxiv`, etc.) control display badges and buttons
- **`_data/`** — YAML data files consumed by Liquid templates

## Deployment

Pushing to `main` triggers `.github/workflows/deploy.yml`, which builds and pushes to `gh-pages`. GitHub Pages serves from `gh-pages`. Do not commit directly to `gh-pages`.

Deploy specifics worth knowing:

- The workflow only runs when files matching its `paths` filter change (`assets/**`, `_sass/**`, `**.bib`, `**.html`, `**.js`, `**.liquid`, `**/*.md`, `**.yml`, `Gemfile*`); doc files (README, INSTALL, etc.) are explicitly excluded.
- The production build (`JEKYLL_ENV=production`) runs a **PurgeCSS** pass (`purgecss.config.js`) that strips CSS classes not found in the built `_site/**/*.html` + `*.js`. **Gotcha:** class names assembled dynamically at runtime (e.g. string-concatenated in JS) can be purged; add them to a safelist if styles go missing only in production.
- The build also depends on ImageMagick and `nbconvert` (for image processing and Jupyter-notebook blog posts).

### Bots that commit back to the repo

- `update-citations.yml` runs `bin/update_scholar_citations.py` to refresh Google Scholar citation counts in `_bibliography/papers.bib`.
- `update-tocs.yml` regenerates Markdown tables of contents.
  Expect these to create automated commits/PRs; avoid hand-editing the regions they own.
