# Agent and contributor briefing — docs.waddleview.com

## Scope

1. **Docs-only repository** — Markdown under [`docs/`](docs/), config in [`mkdocs.yml`](mkdocs.yml). Do **not** add product application code here; behavior changes belong in **[dukk/waddle-view](https://github.com/dukk/waddle-view)**.
2. **Source of truth** — product behavior is documented from waddle-view (see [`DESIGN.md`](DESIGN.md) sync model). Prefer updating existing pages over adding parallel docs.
3. **Strict build** — before finishing a docs task, run **`mkdocs build --strict`** from the repo root (same as CI). Fix broken links and nav references.
4. **Secrets** — never commit API keys, instance ids, or backup archives. Do not paste real credentials into examples.
5. **Design doc** — read [`DESIGN.md`](DESIGN.md) for information architecture, narrative conventions, and product→docs mapping.
6. **Sub-agents / delegated tasks** — include explicit **paths** under `docs/`, **deliverable**, and **forbidden paths** (e.g. do not edit `mkdocs.yml` unless nav changes are requested).

## Commands (from repo root)

Requires **Python 3.10+**.

```bash
python -m venv .venv
# Windows: .venv\Scripts\activate
# macOS/Linux: source .venv/bin/activate
pip install -r requirements.txt
mkdocs serve              # preview http://127.0.0.1:8000
mkdocs build --strict     # CI parity — required before merge
```

## When waddle-view changes

Update the matching docs in the **same task** when possible, or open a follow-up PR. Use the mapping in [`DESIGN.md`](DESIGN.md) and [`docs/help/contributing.md`](docs/help/contributing.md).

| Product change | Docs page |
|----------------|-----------|
| New `/v1/*` route | `docs/api/reference.md` (+ `docs/api/overview.md` if new group) |
| Curator configuration / active APIs | `docs/api/reference.md`, `docs/using/controller.md` |
| New `screen_type` | `docs/reference/screens.md` |
| New `integration_type` | `docs/reference/integrations.md` |
| New env var | `docs/reference/configuration.md` |
| SaaS / Postgres | `docs/reference/configuration.md`, `docs/getting-started/architecture.md` |
| `waddlectl` command | `docs/reference/waddlectl.md` |
| Controller / display UX | `docs/using/controller.md`, `docs/using/display.md` |
| Secret storage model | `docs/using/security.md`, `docs/reference/integrations.md` |

**Primary waddle-view files to read:**

- `apps/waddle_display/README.md`
- `apps/waddle_controller/README.md`
- `apps/waddle_display/.env.example`

## Writing rules

1. **Operator-first** — describe what users do in the controller and what appears on the TV; put REST paths in API/reference pages.
2. **Consistent persistence story** — SQLite default; optional Postgres via `WADDLE_DISPLAY_DATABASE_URL`; integration keys via controller UI (not `WADDLE_DISPLAY_*` provider env).
3. **Link, don’t duplicate** — use `extra.product_repo` / full GitHub URLs for source files; avoid copying large tables from READMEs unless the public site needs them standalone.
4. **MkDocs Material** — use admonitions (`!!! note`, `!!! warning`) sparingly; mermaid and tables are supported (see `mkdocs.yml` extensions).
5. **Nav changes** — only edit [`mkdocs.yml`](mkdocs.yml) `nav:` when adding or renaming pages; keep tab groupings stable unless asked.
6. **Assets** — new screenshots go under `docs/assets/screenshots/`; brand assets under `docs/assets/brand/`. Optimize size; no secrets in images.
7. **Code citations in chat** — when referencing repo files, use `startLine:endLine:filepath` format for the IDE.

## Files agents touch most often

| File | When |
|------|------|
| `docs/api/reference.md` | REST additions, query params, response fields |
| `docs/api/overview.md` | New endpoint groups |
| `docs/using/controller.md` | Operator SPA workflows |
| `docs/using/display.md` | TV runtime, slides, ticker, live preview |
| `docs/reference/integrations.md` | Collectors, secrets, setup steps |
| `docs/reference/configuration.md` | Env vars, KV keys, systemd examples |
| `docs/reference/screens.md` / `ticker.md` | Type catalogs and config_json highlights |
| `docs/getting-started/architecture.md` | Persistence, data flow, SaaS |
| `docs/help/contributing.md` | Sync table for contributors |

## CI

Push to **`main`** runs [`.github/workflows/docs.yml`](.github/workflows/docs.yml): install requirements → `mkdocs build --strict` → deploy `site/` to GitHub Pages.

## Product repo quality (cross-repo)

If the task also changes **waddle-view** code, follow **[waddle-view `AGENTS.md`](https://github.com/dukk/waddle-view/blob/main/AGENTS.md)** — `python scripts/waddle_checks.py full` (and `--controller` when needed). Do not commit `.env`, keys, or instance id files there either.

## Before committing (this repo)

1. `mkdocs build --strict` passes.
2. New pages are listed in `mkdocs.yml` `nav` if they should appear in the sidebar.
3. Internal links use relative paths appropriate for MkDocs (`../` between sections).
4. No secrets or live host-specific credentials in examples.

## Optional: paired workspaces

The repo may include a VS Code multi-root workspace file pointing at **docs.waddleview.com** and **waddle-view** for side-by-side editing. Product changes still land in waddle-view; doc updates land here.
