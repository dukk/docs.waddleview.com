# Configuration reference

Environment variables and config keys control binding, TLS, CORS, provider API keys, plugins, and display behavior.

## HTTP server

| Variable | Default | Description |
|----------|---------|-------------|
| `WADDLE_DISPLAY_HTTP_PORT` | `8787` | Listen port |
| `WADDLE_DISPLAY_HTTP_BIND_IP` | `0.0.0.0` | Bind address |
| `WADDLE_DISPLAY_HTTP_TLS` | `1` | `0` = plain HTTP |
| `WADDLE_DISPLAY_HTTP_TLS_DIR` | app-support `tls/` | Certificate directory |
| `WADDLE_DISPLAY_HTTP_TLS_CERT` | — | PEM cert path override |
| `WADDLE_DISPLAY_HTTP_TLS_KEY` | — | PEM key path override |
| `WADDLE_DISPLAY_HTTP_CORS_ORIGINS` | — | Comma-separated exact origins (seed at startup) |

## Plugins

| Variable | Description |
|----------|-------------|
| `WADDLE_DISPLAY_PLUGINS_DIR` | Directory of plugin folders (`/opt/waddle-view/plugins/<id>/`) |

When unset or empty, `GET /v1/health` reports `plugins_dir_configured: false` and the controller hides plugin navigation.

## Upgrade (Pi)

| Variable | Description |
|----------|-------------|
| `WADDLE_DISPLAY_UPGRADE_SCRIPT` | Path to `waddle-view-upgrade.sh` for in-band upgrades |

## Provider API keys (examples)

Set in systemd `Environment=` or debug `.env` (see [`apps/waddle_display/.env.example`](https://github.com/dukk/waddle-view/blob/main/apps/waddle_display/.env.example)):

| Variable | Used by |
|----------|---------|
| `WADDLE_DISPLAY_OPENAI_API_KEY` | OpenAI joke/trivia/general |
| `WADDLE_DISPLAY_OPEN_WEATHER_MAP_API_KEY` | OpenWeatherMap |
| `WADDLE_DISPLAY_PEXELS_API_KEY` | Pexels photo/video |
| `WADDLE_DISPLAY_FINHUB_API_KEY` | Finnhub stocks |
| `WADDLE_DISPLAY_FLICKR_API_KEY` | Flickr |
| `WADDLE_DISPLAY_GOOGLE_CLIENT_ID` | Google OAuth (public client id) |
| `WADDLE_DISPLAY_MICROSOFT_GRAPH_CLIENT_ID` | Microsoft OAuth |

OAuth **tokens** are not env vars — they live in **SecretStore** after device sign-in.

## Display settings (REST)

`GET/PUT /v1/display/settings` keys include:

| Key | Purpose |
|-----|---------|
| `display_theme_id` | Active theme (`navy_coral` default) |
| `display_custom_themes` | Custom theme array |
| `display_text_scale_screen` | Slide text scale |
| `display_text_scale_ticker` | Ticker text scale |
| `display_timezone` | IANA timezone (empty = default) |
| `controller_time_format` | `12h` or `24h` |
| `controller_date_order` | `mdy`, `dmy`, or `ymd` |
| `adoption_allowed_roles` | JSON array of roles allowed for public adoption |
| `adoption_allow_new_requests` | Legacy boolean (maps to role list) |

## Arbitrary key-values

```http
GET /v1/config/key-values
PUT /v1/config/key-values
DELETE /v1/config/key-values?key=...
```

Notable keys:

| Key | Purpose |
|-----|---------|
| `display.overlay.enabled` | `false` disables all overlays |

## Controller BFF

| Variable | Default | Purpose |
|----------|---------|---------|
| `WADDLE_CONTROLLER_AUTH_ENABLED` | `0` | Multi-user sign-in |
| `WADDLE_CONTROLLER_SESSION_SECRET` | — | Session signing (required when auth on) |
| `WADDLE_CONTROLLER_DATA_DIR` | `./data` | BFF SQLite path |
| `WADDLE_CONTROLLER_BIND` | `127.0.0.1` | BFF listen host |
| `WADDLE_CONTROLLER_PORT` | `5199` | BFF port |
| `WADDLE_CONTROLLER_TLS` | `1` | BFF HTTPS |
| `WADDLE_CONTROLLER_PROXY_UPSTREAM_TIMEOUT_MS` | `180000` | Display proxy timeout |

## systemd example

```ini
[Service]
Environment=WADDLE_DISPLAY_OPENAI_API_KEY=sk-...
Environment=WADDLE_DISPLAY_HTTP_CORS_ORIGINS=https://192.168.1.10:5173
ExecStart=/opt/waddle-view/bundle/waddle_display
```

## Instance id (packaged)

| Path | Purpose |
|------|---------|
| `/etc/waddle-view/instance.id` | Adoption HMAC secret (reference install) |
| App support `waddle_instance.id` | Created on first launch (dev) |

## Next steps

- [Security model](../using/security.md)
- [Pi install](../raspberry-pi/install.md)
