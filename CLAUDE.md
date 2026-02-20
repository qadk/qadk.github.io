# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Build & Dev Commands

```bash
hugo server -D        # Local dev server (includes drafts)
hugo --minify          # Production build
```

- Hugo version: **0.155.2 extended** (pinned in `.github/workflows/deploy.yml`)
- Theme (PaperMod) is a git submodule — clone with `--recurse-submodules`

## Deployment

- Push to `master` triggers GitHub Actions → builds with `hugo --minify` → deploys to GitHub Pages
- Base URL: https://qadk.github.io/

## Architecture

- **Theme**: PaperMod (submodule at `themes/PaperMod/`), no direct modifications to theme files
- **Custom layouts** (project-level overrides):
  - `layouts/_default/_markup/render-codeblock-mermaid.html` — renders ` ```mermaid ` blocks as diagrams
  - `layouts/partials/extend_head.html` — conditionally loads Mermaid JS from CDN, with dark/light theme detection

## Content

- Posts live in `content/posts/` with filename format: `YYYY-MM-DD-slug.md`
- Language: zh-tw (Traditional Chinese, Taiwan usage)
- Required frontmatter (YAML):
  ```yaml
  ---
  title: "文章標題"
  date: YYYY-MM-DD
  draft: false
  tags: ["tag1", "tag2"]
  ---
  ```
- Mermaid diagrams work natively via fenced code blocks (` ```mermaid `)
- Images go in `static/images/`

## Writing Style

- 段落和粗體標題結尾不加「。」，段落中間的句號保留
