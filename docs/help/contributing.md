# Contributing

Documentation improvements are welcome in **[dukk/docs.waddleview.com](https://github.com/dukk/docs.waddleview.com)**. Product code and behavior changes belong in **[dukk/waddle-view](https://github.com/dukk/waddle-view)**.

## Edit this site

1. Fork and clone `docs.waddleview.com`.
2. Install dependencies and preview locally (see the [repository README](https://github.com/dukk/docs.waddleview.com#local-development)).
3. Edit Markdown under `docs/`.
4. Open a pull request — CI runs `mkdocs build --strict`.

Use the **Edit this page** link on any doc page to jump to the source file on GitHub.

## Documentation in this repo

Read [`AGENTS.md`](https://github.com/dukk/docs.waddleview.com/blob/main/AGENTS.md) and [`DESIGN.md`](https://github.com/dukk/docs.waddleview.com/blob/main/DESIGN.md) before large doc edits.

## Product contributions

Read [`AGENTS.md`](https://github.com/dukk/waddle-view/blob/main/AGENTS.md) before large changes to waddle-view.

**Quality bar** (waddle-view repo):

```bash
python scripts/waddle_checks.py full
```

- Flutter tests with **≥ 80%** line coverage on gated packages
- Controller Vitest coverage when touching `waddle_controller`

**Do not commit**: `.env`, API keys, `waddle_instance.id`, backup archives with secrets.

## Keeping docs in sync

When you change REST routes, screen types, or integrations in waddle-view, update the matching pages here:

| Product change | Docs page |
|----------------|-----------|
| New `/v1/*` route | `docs/api/reference.md` |
| Curator configuration / active APIs | `docs/api/reference.md`, `docs/using/controller.md` |
| New `screen_type` | `docs/reference/screens.md` |
| New integration id | `docs/reference/integrations.md` |
| New env var | `docs/reference/configuration.md` |
| SaaS / Postgres deployment | `docs/reference/configuration.md`, `docs/getting-started/architecture.md` |
| `waddlectl` command | `docs/reference/waddlectl.md` |
| Controller operator UX | `docs/using/controller.md`, `docs/using/display.md` |

Prefer documenting stable operator-facing behavior; link to source files for implementation detail.

## Releases

User-facing install and upgrade docs: [Pi install](../raspberry-pi/install.md), [Upgrade](../raspberry-pi/upgrade.md). Changelog: [GitHub Releases](https://github.com/dukk/waddle-view/releases).
