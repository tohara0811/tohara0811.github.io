# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

@AGENTS.md

## Essential Commands

```bash
# Start local dev server (recommended)
docker compose pull && docker compose up
# Site runs at http://localhost:8080

# Rebuild after Gemfile/Dockerfile changes
docker compose up --build

# Stop and free port 8080
docker compose down

# Format all files before committing (mandatory — CI will fail otherwise)
npm install --save-dev prettier @shopify/prettier-plugin-liquid  # first time only
npx prettier . --write
```

## Architecture Overview

This is an **al-folio** Jekyll academic portfolio theme deployed to GitHub Pages. The build pipeline is:

1. Source files on `main` branch → GitHub Actions builds with `bundle exec jekyll build` → output committed to `gh-pages` branch → served by GitHub Pages.
2. Never edit `gh-pages` directly; it is auto-generated.

### How content flows

- **`_config.yml`** is the single source of truth for site-wide settings, feature flags, URL/baseurl, and plugin toggles. Changing something here affects the whole site.
- **`_data/*.yml`** supplies structured data: `socials.yml` (social links), `cv.yml` (RenderCV format), `coauthors.yml`, `venues.yml`, `repositories.yml`.
- **`_bibliography/papers.bib`** holds all publications in BibTeX; rendered by `jekyll-scholar` with author highlighting driven by `scholar:last_name`/`scholar:first_name` in `_config.yml`.
- **`_includes/`** + **`_layouts/`** are Liquid templates. Layouts (`about.liquid`, `post.liquid`, `cv.liquid`, `distill.liquid`) pull in components from `_includes/`.
- **`_sass/`** holds SCSS. Theme accent color lives in `_sass/_themes.scss` (`--global-theme-color`); variable palette in `_sass/_variables.scss`.
- **`assets/`** holds static files: `img/`, `pdf/`, `json/resume.json` (JSONResume), `rendercv/` (RenderCV config + generated PDFs).

### Content directories

| Directory | Purpose |
|---|---|
| `_pages/` | Static pages (about, cv, publications, projects, teaching…) |
| `_posts/` | Blog posts — filename must be `YYYY-MM-DD-title.md` |
| `_projects/` | Project showcase cards |
| `_news/` | Short announcements shown on home page |
| `_teachings/` | Course/teaching pages |

## Critical Configuration

`_config.yml` url/baseurl **must** be set correctly or assets won't load:
- Personal site: `url: https://tohara0811.github.io` + `baseurl:` (empty, but the key must exist)
- Quote values containing `:` or `&` to avoid YAML parse errors.

## CI/CD Checks

Two CI checks block merges:
- **prettier.yml** — formatting; fix with `npx prettier . --write`
- **deploy.yml** — full Jekyll build; test locally with `docker compose up --build` before pushing

## File-type Specific Instructions

See `.github/instructions/` for path-specific rules:
- `markdown-content.instructions.md` — posts, pages, projects
- `yaml-configuration.instructions.md` — `_config.yml`, `_data/`
- `bibtex-bibliography.instructions.md` — `_bibliography/`
- `liquid-templates.instructions.md` — `_includes/`, `_layouts/`
- `javascript-scripts.instructions.md` — `_scripts/`
