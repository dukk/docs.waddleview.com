# Operator controller

**waddle_controller** is the browser UI for managing one or more displays. It pairs via the adoption API, then proxies all display REST traffic through an optional **BFF** (Hono + SQLite) so the browser never talks to self-signed display TLS directly.

![Controller curators screen](../assets/screenshots/controller/curators-1.png){ width="640" }

## Development

From `apps/waddle_controller/` in the waddle-view repo:

```bash
npm ci
npm run dev
```

- SPA: **https://127.0.0.1:5173**
- BFF: **https://127.0.0.1:5199** (proxies `/bff/v1/proxy/*` to each display)

Set `WADDLE_CONTROLLER_TLS=0` for plain HTTP in dev.

## Pairing a display

![Pairing flow](../assets/pairing.png){ width="480" }

1. Add the display URL in the controller (LAN HTTPS, e.g. `https://192.168.1.50:8787`).
2. **Request adoption** — `POST /v1/adoption/request` with an identifier and role.
3. Read the **XXXX-XXXX** challenge on the TV (security alert overlay).
4. **Confirm** — `POST /v1/adoption/confirm` with the code; store the returned `api_key`.
5. The display records your browser **Origin** in `cors_allowed_origins` for later API calls.

Admins can issue keys instantly with an existing admin bearer token. See [Security](security.md) and [API overview](../api/overview.md).

## Main workflows

| Area | What you configure |
|------|-------------------|
| **Programs / Curators** | Screen rotation, dwell, scheduling |
| **Integrations** | Enable providers, API keys, OAuth accounts |
| **Data** | Browse and moderate jokes, news, photos, etc. |
| **Ticker** | Tape types, weights, ordering |
| **Overlays** | Celebration schedules and assets |
| **Display settings** | Theme, timezone, adoption policy, backup |
| **Controller settings** | Users, scheduled backups, Pi upgrade |

![Integrations list](../assets/screenshots/controller/integrations-1.png){ width="640" }

Schema-driven forms use JSON Schema from `GET /v1/meta/config-schemas` — prefer caching that response once per session.

## Multi-display and catalog copy

The controller can manage multiple adopted displays. Catalog copy flows duplicate screens, overlays, or integration settings between displays (admin/operator permissions apply).

## Backup and restore

- **Display settings → Backup & restore** — pull or push archives via display admin API (`display.maintenance`)
- **Controller settings → Backup & restore** — scheduled pulls into BFF storage, retention, restore to display

Archives match **waddlectl** layout (`manifest.json`, `db/`, `media/`).

## Pi in-band upgrade

When `GET /v1/health` reports `upgrade_capable: true`, the controller can trigger `POST /v1/display/ops/upgrade` (requires upgrade script + passwordless sudo). See [Pi upgrade](../raspberry-pi/upgrade.md).

## Optional BFF authentication

Enable `WADDLE_CONTROLLER_AUTH_ENABLED=1` and `WADDLE_CONTROLLER_SESSION_SECRET` for multi-user sign-in. Displays and API keys are stored per account in `user_displays`.

## Next steps

- [Security model](security.md)
- [REST API reference](../api/reference.md)
- [waddlectl CLI](../reference/waddlectl.md)
