# Security model

Waddle View is designed for **local-first** operation: the display REST API listens on your LAN, data stays on the device, and credentials are not stored in cleartext in SQLite.

## API authentication

| Route class | Auth |
|-------------|------|
| `GET /v1/health` | Public |
| `POST /v1/adoption/request`, `POST /v1/adoption/confirm` | Public (LAN CORS policy) |
| All other `/v1/*` | `Authorization: Bearer <api_key>` |

API keys are prefixed with **`wd_`**. Only a **SHA-256 hash** is stored in SQLite (`api_clients`). Re-adopting the same identifier **rotates** the key.

### Roles and permissions

| Role | Typical use |
|------|-------------|
| **admin** | Full configuration, backups, user management, adoption client admin |
| **operator** | Integrations, screens, overlays, curator writes |
| **power_viewer** | Catalog read, navigation control, live preview |
| **viewer** | Telemetry read only |

`GET /v1/display/settings` exposes `adoption_allowed_roles` — restrict which roles public adoption may request.

**Instance id** (`waddle_instance.id`) is the HMAC secret for adoption; it is **not** the API key. Packaged installs: `/etc/waddle-view/instance.id`.

## Integration secrets

| Secret type | Storage |
|-------------|---------|
| Static provider API keys (OpenAI, Pexels, Finnhub, …) | Process **environment** / debug `.env` — never SQLite |
| Google / Microsoft OAuth tokens | **SecretStore** (DPAPI on Windows, Secret Service on Linux) |
| Operator-configured integration secrets | **AES-GCM** in SQLite (`integration_secrets`), DEK wrapped with machine binding |

Never commit `.env`, instance id files, API keys, or backup archives containing secrets.

## CORS

Browser clients (controller) send `Origin` or `Referer`. Adoption routes allow private-network origins. Other routes require the origin in `cors_allowed_origins` (from successful adoption or `WADDLE_DISPLAY_HTTP_CORS_ORIGINS`).

## TLS

Default: HTTPS on **8787** with a self-signed cert under app-support `tls/`. Set `WADDLE_DISPLAY_HTTP_TLS=0` for plain HTTP in trusted dev loops only.

For untrusted networks, terminate TLS at a reverse proxy with a public certificate.

## Controller BFF

When auth is enabled, session cookies are signed with `WADDLE_CONTROLLER_SESSION_SECRET`. Display API keys stored in the BFF database are encrypted per deployment.

## Content moderation

`content.moderate` permission is required to suppress or delete catalog items (`PATCH` / `DELETE /v1/content/*`). Do not put secrets in alert bodies.

## Next steps

- [API overview](../api/overview.md)
- [Configuration reference](../reference/configuration.md)
