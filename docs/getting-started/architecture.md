# Architecture

Waddle View runs as a **single process**: Flutter UI, background data collection, and an embedded Shelf HTTP server share one isolate. The composition root is `apps/waddle_display/lib/main.dart`.

## Design principles

- **Ports and adapters** — abstract boundaries (`IDataProvider`, `BlobStore`, `SecretStore`, repositories) with Drift/filesystem implementations
- **Drift as the hub** — screens, integrations, overlays, alerts, and config live in **SQLite** by default, or **PostgreSQL** when `WADDLE_DISPLAY_DATABASE_URL` is set
- **Integration API keys in the database** — provider keys are configured in the controller and stored encrypted on the display; legacy `WADDLE_DISPLAY_*` env keys are ignored
- **OAuth tokens** — **SecretStore** (not SQLite cleartext)
- **Ticker in memory** — curated marquee text is not persisted; REST exposes a read-only snapshot
- **Optional SaaS feed** — when `WADDLE_SAAS_MODE=1`, the display can consume a remote cloud feed (SSE) instead of local collectors

## Runtime diagram

![Waddle View architecture](../assets/architecture.svg)

## Module map

```mermaid
flowchart TB
  subgraph ui [Presentation]
    Shell[DashboardShell]
    Rotator[ScreenRotator]
    Ticker[TickerMarquee]
    Overlays[AlertOverlayHost]
  end
  subgraph loops [Background]
    Engine[DataCollectionEngine]
    Curator[DefaultDashboardCurator]
  end
  subgraph api [Embedded HTTP]
    REST[LocalRestServer]
  end
  subgraph data [Persistence]
    DB[(SQLite or Postgres)]
    Blobs[media/ blobs]
  end
  Shell --> Rotator
  Shell --> Ticker
  Shell --> Overlays
  Engine --> DB
  Engine --> Blobs
  Curator --> Ticker
  REST --> DB
  Engine --> Curator
```

Blob files (`media/`) always live on disk next to application support, regardless of database backend.

## Startup sequence

1. Open database (SQLite file or Postgres), run migrations, seed defaults
2. Load `SecretStore` and merged environment for `ProviderConfigResolver`
3. Start **DataCollectionEngine** (sequential provider runs), or **SaaS feed sync** when cloud mode is enabled
4. Run initial **curator** refresh (builds slide program + ticker snapshot)
5. Start **LocalRestServer** on `0.0.0.0:8787` (TLS by default)
6. `runApp` — `ScreenRotator`, `TickerMarquee`, overlay host

On dispose: stop engine, close HTTP server, close database.

## Data flow

1. **Collectors** (`waddle_integrations`) fetch external data and write rows + blobs via `DataWriteContext` (skipped in SaaS mode)
2. **Curator** reads the database + schedule rules to produce the active **slide program** and **ticker items**
3. **UI** renders the current slide; ticker scrolls independently; overlays schedule by calendar rules
4. **Controller / REST** mutates configuration; display hot-reloads programs on the next curator cycle

## Related docs

- [Display runtime](../using/display.md)
- [REST API overview](../api/overview.md)
- [Security model](../using/security.md)
- [Configuration reference](../reference/configuration.md)
- Upstream: [ARCHITECTURE.md](https://github.com/dukk/waddle-view/blob/main/apps/waddle_display/ARCHITECTURE.md)
