# Documentation

Central docs for products maintained by **Mitchies1970**. Each product lives in its own folder under `docs/` and appears in the list below once published.

## Products

_No products published yet._ Add one using the steps under [How to add a product](#how-to-add-a-product), then link it here.

<!-- Example when a product exists:

- **[Product Name](product-slug/index.md)** — short one-line description

Or Material grid cards (see Material “grids” docs) if several products need equal visual weight.

-->

## Path convention

| On disk | Site URL |
| --- | --- |
| `docs/<product-slug>/index.md` | `/<product-slug>/` |
| `docs/<product-slug>/guides/getting-started.md` | `/<product-slug>/guides/getting-started/` |
| `docs/<product-slug>/reference/cli.md` | `/<product-slug>/reference/cli/` |
| `docs/<product-slug>/api/index.md` | `/<product-slug>/api/` |

Use a lowercase kebab-case **product slug** (for example `billing-api`). Keep the usual section folders: `guides/`, `reference/`, `api/`.

## How to add a product

1. Create `docs/<product-slug>/` with at least `index.md` (product landing).
2. Add guides, reference, and API pages as needed under that folder.
3. Register every new page in `mkdocs.yml` `nav` under a top-level product entry.
4. Link the product from this home page (cards or a short list).
5. Run `mkdocs build --strict`, then open a PR.

See [Contributing](contributing.md) for nav examples and the document-product skill checklist.
