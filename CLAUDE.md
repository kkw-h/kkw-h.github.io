# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a [Hexo](https://hexo.io/) v8.1.1 static site blog hosted on GitHub Pages at https://kkw-h.github.io. The blog uses the `hexo-theme-aircloud` theme and is written in Chinese (zh).

## Common Commands

```bash
# Development server (runs on http://localhost:4000)
npm run server

# Generate static files (output to `public/`)
npm run build

# Clean cache and generated files
npm run clean

# Create a new post
npx hexo new "Post Title"

# Create a new page (e.g., tags or about)
npx hexo new page "Page Name"
```

## Project Structure

- `source/_posts/` - Blog posts organized in subdirectories:
  - `life/` - Lifestyle posts
  - `tech/backend/` - Backend technology posts
  - `tech/devops/` - DevOps posts
  - `tech/mobile/` - Mobile development posts
  - `tech/workflow/` - Workflow/tool posts
  - `tools/` - Tool recommendations
- `source/about/` - About page
- `source/tags/` - Tags index page
- `source/images/` - Static images
- `themes/hexo-theme-aircloud/` - Theme files
- `public/` - Generated static site (gitignored, deployed to GitHub Pages)
- `scaffolds/` - Templates for new posts/pages

## Post Frontmatter Format

```yaml
---
title: Post Title
date: YYYY-MM-DD HH:mm:ss
categories:
  - Technology
  - Backend
tags:
  - Golang
  - Optimization
---
```

Categories use a hierarchy (e.g., `Technology` → `Backend`). Posts are organized in folders but Hexo discovers them recursively from `source/_posts/`.

## Theme Configuration

The theme is configured in the root `_config.yml` (not in the theme folder). Key configurations:

- `theme: hexo-theme-aircloud` - Sets the active theme
- `permalink: :hash.html` - Uses hash-based URLs
- `search:` - Configures `hexo-generator-search` for site search
- `comment:` - Giscus comments configuration (linked to `kkw-h/kkw-h.github.io` repo)
- `post_style.indent: 0` - Disables first-line indentation
- `avatar_style.radius: true` - Enables avatar rounded corners

## Deployment

GitHub Actions (`.github/workflows/pages.yml`) automatically builds and deploys to GitHub Pages on every push to `main`. The workflow:
1. Installs npm dependencies
2. Runs `npm run build`
3. Deploys the `public/` directory to GitHub Pages

Do not commit the `public/` directory; it is generated during CI.

## Creating Posts

When creating new posts, follow the existing folder structure:
- Technical posts go in `source/_posts/tech/<subcategory>/`
- Lifestyle posts go in `source/_posts/life/<subcategory>/`
- Tool recommendations go in `source/_posts/tools/`

Use `npx hexo new "Title"` to create posts at the root, then move the file to the appropriate subdirectory.
