# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This is a Jekyll-based static site built for GitHub Pages, showcasing bioinformatics and microbiome analysis work. The site uses the Minimal Mistakes theme and is written in Korean and English.

## Architecture & Structure

### Jekyll Configuration
- **Theme**: Uses `mmistakes/minimal-mistakes` remote theme
- **Build System**: Jekyll with GitHub Pages compatibility
- **Dependencies**: Managed through `Gemfile` using `github-pages` gem bundle
- **Content Types**: 
  - Static pages in `_pages/` directory (about, projects, stack)
  - Blog posts in `_posts/` directory (currently empty)
  - Project showcases in `projects/` directory
  - Site-wide navigation defined in `_data/navigation.yml`

### Content Organization
- **Main Pages**: About (`/about/`), Projects (`/projects/`), Tech Stack (`/stack/`)
- **Featured Project**: FunOMIC2 Nextflow pipeline conversion project
- **Focus Areas**: Bioinformatics, microbiome analysis, metagenomics, reproducible research workflows

### Site Configuration
- **Base URL**: `https://e4m-hash.github.io`
- **Language**: Mixed Korean/English content
- **Features**: Search enabled, author profile, Jekyll plugins for pagination and feeds
- **Markdown**: kramdown processor with rouge syntax highlighter

## Development Commands

### Local Development
```bash
# Install dependencies
bundle install

# Serve site locally
bundle exec jekyll serve

# Build site
bundle exec jekyll build
```

### Content Management
```bash
# Create new post
# Add markdown file to _posts/ with format: YYYY-MM-DD-title.md

# Create new project
# Add markdown file to projects/ directory

# Update navigation
# Edit _data/navigation.yml
```

## Key Technical Context

### Research Focus
- **Primary Domain**: Microbiome metagenomics analysis
- **Tools**: Python, R, C++, Nextflow, Docker
- **Platforms**: Linux, AWS, GCP, Azure
- **Specializations**: Statistical analysis (PERMANOVA, PCoA), machine learning (Random Forest, SHAP), workflow automation

### Featured Technologies
- **Pipeline Development**: Nextflow DSL2, containerization (Docker/Singularity)
- **Data Analysis**: Alpha/Beta diversity, multivariate statistics, functional prediction
- **Infrastructure**: HPC environments, multi-cloud deployment, reproducible research practices

### Content Guidelines
- Site content is bilingual (Korean primary, some English technical terms)
- Technical documentation should emphasize reproducibility and automation
- Project descriptions should highlight practical applications in bioinformatics research
- Maintain academic/research professional tone while being accessible