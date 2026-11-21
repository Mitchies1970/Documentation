---
name: document-product
description: >-
  Add or update product docs in the Mitchies1970/Documentation MkDocs Material
  site. Use when documenting an app from another repo into this central docs
  repo, writing guides/reference/API pages, updating mkdocs.yml nav, or
  preparing a docs PR for GitHub Pages.
---

# Document a product (MkDocs Material)

Central docs live in **this** repo (`Mitchies1970/Documentation`). Product code often lives elsewhere — gather facts from the source app, write Markdown here, update nav, verify the build, open a PR.

**Install note:** This skill ships under `.cursor/skills/` so it can be copied or referenced. A **user- or team-level** skill install is better than per-repo copies if Michael chooses that later.

## When to use

- Documenting a product (new or existing) into this site
- Porting README / in-app docs from another repo into MkDocs
- Extending guides, reference, or API pages and nav

## Site facts

| Item | Value |
| --- | --- |
| Live site | `https://mitchies1970.github.io/Documentation/` |
| Config | `mkdocs.yml` |
| Content root | `docs/` |
| Theme | Material (tabs, sections, expand, top nav, search, code copy/annotate, edit, linked content tabs) |
| Deploy | `.github/workflows/deploy-docs.yml` — build on `main` / `workflow_dispatch`, `mkdocs build --strict`, GitHub Pages |
| Extensions in use | admonitions, details, Mermaid (`mermaid` fences), highlight, tabbed (`===`), attr_list, toc permalinks |

Do not invent a second docs host or deploy path.

## Layout

### Single product (current template)

Flat sections under `docs/`:

```text
docs/
  index.md
  guides/          # how-to / tutorials
  reference/       # architecture, CLI, config, glossary
  api/             # REST/API surface
```

Nav in `mkdocs.yml` mirrors those folders (Home / Guides / Reference / API).

### Multi-product (when a second product arrives)

Prefer **product-scoped folders** under `docs/`. Migrate the first product out of the flat root when adding the second — do not leave one product flat and another nested.

```text
docs/
  index.md                 # hub: short intro + links to each product
  <product-slug>/
    index.md
    guides/
    reference/
    api/
```

Nav pattern:

```yaml
nav:
  - Home: index.md
  - Product Name:
      - Overview: product-slug/index.md
      - Guides:
          - Getting started: product-slug/guides/getting-started.md
      - Reference:
          - Architecture: product-slug/reference/architecture.md
      - API:
          - Overview: product-slug/api/index.md
```

- `<product-slug>`: lowercase kebab-case (`acme-app`, `billing-api`)
- Keep section names stable across products: **Guides**, **Reference**, **API**
- Shared cross-product pages (rare) stay at `docs/` root and appear under Home or a top-level Shared nav entry

Until a second product exists, keep the flat single-product layout unless the user asks to restructure.

## File placement & naming

| Content | Put it in | Filename |
| --- | --- | --- |
| Install / configure / operate | `guides/` | kebab-case verb phrases (`getting-started.md`, `how-to-deploy.md`) |
| Architecture, CLI, config keys, glossary | `reference/` | short nouns (`architecture.md`, `cli.md`, `config.md`) |
| HTTP/API resources | `api/` | resource or `index.md` for overview |
| Product landing | `index.md` (root or product folder) | — |

Rules:

- One topic per file; split large pages instead of endless scrolling
- Relative links between docs (`../reference/architecture.md`)
- Match Material patterns already in the template: admonitions (`!!! note`), tabs (`=== "pip"`), Mermaid, `{ .md-button }` CTAs on landings
- Update **every** new/renamed page in `mkdocs.yml` `nav` — orphan files are invisible

## Workflow

1. **Scope** — product name, slug, which sections (guides / reference / api), source repo path or URL.
2. **Gather facts from the product repo** — README, CLI `--help`, OpenAPI/routes, config schema/env samples, architecture notes. Prefer code and real commands over marketing copy. Do not invent endpoints or flags.
3. **Write or update Markdown** in the correct folder (flat or `<product-slug>/…`).
4. **Update `mkdocs.yml`** — `nav` entries; only change `site_name` / `site_description` when intentionally rebranding the whole site (multi-product: site name stays generic, product names live in nav/pages).
5. **Cross-link** related guides ↔ reference ↔ API; fix broken links you touch.
6. **Build** when possible:
   ```bash
   pip install mkdocs-material
   mkdocs build --strict
   ```
   Fix warnings/errors before opening the PR.
7. **PR** — branch off `main`, commit docs + `mkdocs.yml`, push, open PR. Merging to `main` deploys via Actions → Pages. Do not force-push.

## Writing conventions

- Lead with the task the reader is doing; keep steps numbered and copy-pasteable
- Use real package/binary names from the product; no leftover `Acme App` / `acme` placeholders for real products
- Prefer tables for config keys, endpoints, and env vars
- Admonitions for warnings/tips; Mermaid for architecture or request flows when they clarify
- Keep secrets out of docs — use placeholders (`<token>`, `.env.example` patterns)

## Do not

- Commit built `site/` output (CI builds it)
- Skip `nav` updates or rely on folder browse alone
- Duplicate the same guide in the product app repo and here without a clear source of truth (prefer this site for published docs; link out for deep code-only notes)
- Add new MkDocs plugins/extensions unless required and wired in `mkdocs.yml` + CI (`pip install`)
- Create README/AGENTS pointers unless the user asks — skill location is enough
- Force-push or push to `main` directly

## Quick checklist

- [ ] Facts verified against the product codebase
- [ ] Files under the right `guides/` / `reference/` / `api/` (and product slug if multi-product)
- [ ] `mkdocs.yml` nav updated
- [ ] Links and Material syntax valid
- [ ] `mkdocs build --strict` passes (when runnable)
- [ ] PR opened against `main`
