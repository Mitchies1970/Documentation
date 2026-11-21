---
name: document-product
description: >-
  Add or update product docs in the Mitchies1970/Documentation MkDocs Material
  site. Use when documenting an app from another repo into this central docs
  repo, writing guides/reference/API pages under a product slug, updating
  mkdocs.yml nav and the home product list, or preparing a docs PR for
  GitHub Pages.
---

# Document a product (MkDocs Material)

Central docs live in **this** repo (`Mitchies1970/Documentation`). Product code often lives elsewhere — gather facts from the source app, write Markdown under a **product slug**, update nav and the home hub, verify the build, open a PR.

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

## Layout (multi-product)

Every product is scoped under `docs/<product-slug>/`. The site home is a hub, not a single-product landing.

```text
docs/
  index.md                 # hub: product list + path convention + how to add
  contributing.md          # fuller nav examples and checklist
  <product-slug>/
    index.md               # product landing
    guides/
    reference/
    api/
```

### URL / path rule

| On disk | Site path |
| --- | --- |
| `docs/<product-slug>/index.md` | `/<product-slug>/` |
| `docs/<product-slug>/guides/getting-started.md` | `/<product-slug>/guides/getting-started/` |
| `docs/<product-slug>/reference/cli.md` | `/<product-slug>/reference/cli/` |
| `docs/<product-slug>/api/index.md` | `/<product-slug>/api/` |

- `<product-slug>`: lowercase kebab-case (`billing-api`, `inventory-ui`)
- Keep section names stable across products: **Guides**, **Reference**, **API**
- Shared cross-product pages (rare) stay at `docs/` root and appear under Home or a top-level Shared nav entry
- Do **not** put product pages flat under `docs/guides/` — always nest under the product slug

### Nav pattern

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

`site_name` / `site_description` stay generic for the whole site. Product names live in nav tabs and page titles.

### Home page links

After adding a product, update `docs/index.md` so the **Products** section links to it. Prefer a short Material-friendly list or [grid cards](https://squidfunk.github.io/mkdocs-material/reference/grids/) — keep the hub uncluttered (name + one-line description). Remove the “no products yet” placeholder once the first product is live.

## File placement & naming

| Content | Put it in | Filename |
| --- | --- | --- |
| Install / configure / operate | `<product-slug>/guides/` | kebab-case verb phrases (`getting-started.md`, `how-to-deploy.md`) |
| Architecture, CLI, config keys, glossary | `<product-slug>/reference/` | short nouns (`architecture.md`, `cli.md`, `config.md`) |
| HTTP/API resources | `<product-slug>/api/` | resource or `index.md` for overview |
| Product landing | `<product-slug>/index.md` | — |
| Site hub / contributing | `docs/index.md`, `docs/contributing.md` | — |

Rules:

- One topic per file; split large pages instead of endless scrolling
- Relative links between docs (e.g. `../reference/architecture.md` from a guide)
- Match Material patterns already in the site: admonitions (`!!! note`), tabs (`=== "pip"`), Mermaid, `{ .md-button }` CTAs on landings
- Update **every** new/renamed page in `mkdocs.yml` `nav` — orphan files are invisible
- Link new products from the home hub

## Workflow

1. **Scope** — product name, slug, which sections (guides / reference / api), source repo path or URL.
2. **Gather facts from the product repo** — README, CLI `--help`, OpenAPI/routes, config schema/env samples, architecture notes. Prefer code and real commands over marketing copy. Do not invent endpoints or flags.
3. **Write or update Markdown** under `docs/<product-slug>/…`.
4. **Update `mkdocs.yml`** — add or extend the product’s nav entry; only change `site_name` / `site_description` when intentionally rebranding the whole site.
5. **Update home** — add/adjust the product link on `docs/index.md`.
6. **Cross-link** related guides ↔ reference ↔ API; fix broken links you touch.
7. **Build** when possible:
   ```bash
   pip install mkdocs-material
   mkdocs build --strict
   ```
   Fix warnings/errors before opening the PR.
8. **PR** — branch off `main`, commit docs + `mkdocs.yml` (+ skill if layout guidance changed), push, open PR. Merging to `main` deploys via Actions → Pages. Do not force-push.

## Writing conventions

- Lead with the task the reader is doing; keep steps numbered and copy-pasteable
- Use real package/binary names from the product; never leave sample placeholders as if they were real products
- Prefer tables for config keys, endpoints, and env vars
- Admonitions for warnings/tips; Mermaid for architecture or request flows when they clarify
- Keep secrets out of docs — use placeholders (`<token>`, `.env.example` patterns)

## Do not

- Commit built `site/` output (CI builds it)
- Skip `nav` updates or rely on folder browse alone
- Skip the home-page product link for a newly published product
- Document a product in flat `docs/guides/` (or similar) instead of `docs/<product-slug>/…`
- Duplicate the same guide in the product app repo and here without a clear source of truth (prefer this site for published docs; link out for deep code-only notes)
- Add new MkDocs plugins/extensions unless required and wired in `mkdocs.yml` + CI (`pip install`)
- Create README/AGENTS pointers unless the user asks — skill location is enough
- Force-push or push to `main` directly

## Quick checklist

- [ ] Facts verified against the product codebase
- [ ] Files under `docs/<product-slug>/guides|reference|api/`
- [ ] `mkdocs.yml` nav updated for the product
- [ ] Home (`docs/index.md`) links to the product
- [ ] Links and Material syntax valid
- [ ] `mkdocs build --strict` passes (when runnable)
- [ ] PR opened against `main`
