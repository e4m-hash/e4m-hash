# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

Jekyll-based static site for GitHub Pages showcasing bioinformatics and microbiome analysis work. Uses the Minimal Mistakes remote theme. Content is bilingual (Korean primary, English technical terms).

## Development Commands

```bash
bundle install                  # install dependencies
bundle exec jekyll serve        # local dev server (http://localhost:4000)
bundle exec jekyll build        # production build into _site/
```

## Architecture & Structure

### Content Layers

There are two separate roles for project content:
- `_pages/projects.md` — the `/projects/` index page listing all projects
- `projects/` — individual project detail pages (e.g., `FunOMIC2.md`)

Similarly, `_pages/about.md` and `_pages/stack.md` are the standalone About and Tech Stack pages. Navigation across these is defined in `_data/navigation.yml`.

Blog posts go in `_posts/` with the filename format `YYYY-MM-DD-title.md`; the directory is currently empty.

### Theme & Styling

The site uses `mmistakes/minimal-mistakes` as a remote theme. Custom styles live in `assets/css/main.scss`, which extends the theme with a bioinformatics color scheme (Sea Green primary, Steel Blue secondary, Gold accent), custom components (`bio-cards`, `tech-grid`, `project-showcase`), and responsive/animation overrides. Edit this file for any visual changes — do not create separate CSS files.

### Jekyll Configuration

- `_config.yml` — main config; sets remote theme, plugins, author profile, per-scope layout defaults
- `Gemfile` — pins `github-pages ~> 231` for Pages compatibility; active plugins: `jekyll-feed`, `jekyll-sitemap`, `jekyll-seo-tag`, `jekyll-include-cache`, `jekyll-remote-theme`
- `CLAUDE.md` and `README.md` are excluded from the Jekyll build via `_config.yml`

### Page Defaults (from `_config.yml`)

- `_posts` scope: `layout: single`, author profile, read time, comments, share, related
- `_pages` scope: `layout: single`, author profile, `classes: wide`

## Content Guidelines

- Korean is the primary language; use English for technical terms (tool names, commands, etc.)
- Maintain an academic/research professional tone
- Project pages should emphasize reproducibility, containerization, and practical bioinformatics applications
