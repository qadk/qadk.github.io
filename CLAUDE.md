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
- Article images go in `static/images/posts/{slug}/` (e.g. `static/images/posts/self-describing-modules/cover.png`)
- No H1 in markdown body — PaperMod generates it from frontmatter `title`, content starts from H2
- `hasCJKLanguage = true` is enabled in `hugo.toml` for accurate word count and reading time
- Cover image frontmatter:
  ```yaml
  cover:
    image: /images/posts/{slug}/cover.png
    alt: "圖片描述"
  ```

## Writing Style

- 段落和粗體標題結尾不加「。」，段落中間的句號保留
- 從業務痛點出發，不是從技術 pattern 出發
- 先給答案再解釋——開頭讓讀者知道做法，再回頭講為什麼
- 用真實的商品、櫃位、區域名稱舉例，不用 TypeA、TypeB
- 用語、範例、程式碼之間要前後一致，不能有矛盾
- 程式碼命名對齊業務語言，讓現場人員也看得懂
- 結尾精簡，一句話夠力就好
- 主動提 trade-off，不只講優點
- 圖表每條線都要符合實際業務邏輯，不為好看亂畫
