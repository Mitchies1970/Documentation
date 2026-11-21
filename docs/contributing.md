# Contributing

How to add or update product documentation in this MkDocs Material site.

## Layout

```text
docs/
  index.md                 # hub — product list + convention
  contributing.md          # this page
  <product-slug>/
    index.md               # product landing
    guides/                # how-to / tutorials
    reference/             # architecture, CLI, config, glossary
    api/                   # REST/API surface
```

Site paths follow the folder tree: `docs/<product-slug>/guides/getting-started.md` → `/<product-slug>/guides/getting-started/`.

## Add a product

1. Pick a **product slug** — lowercase kebab-case (`billing-api`, `inventory-ui`).
2. Create the folder tree and write Markdown (start with `index.md`).
3. Update `mkdocs.yml` `nav`. Example:

```yaml
nav:
  - Home: index.md
  - Contributing: contributing.md
  - Product Name:
      - Overview: product-slug/index.md
      - Guides:
          - Getting started: product-slug/guides/getting-started.md
      - Reference:
          - Architecture: product-slug/reference/architecture.md
      - API:
          - Overview: product-slug/api/index.md
```

4. Link the product from [Home](index.md) (Material cards or a simple list).
5. Verify:

```bash
pip install mkdocs-material
mkdocs build --strict
```

6. Open a PR against `main`. Merging deploys to GitHub Pages.

## Writing tips

- One topic per file; kebab-case filenames (`getting-started.md`, `how-to-deploy.md`).
- Relative links between pages; update `nav` for every new or renamed file.
- Prefer real commands and schemas from the product repo — do not invent APIs or flags.
- Match existing Material patterns: admonitions (`!!! note`), tabs (`===`), Mermaid, `{ .md-button }` CTAs.

Agents documenting a product should follow `.cursor/skills/document-product/SKILL.md`.
