# Display runtime

The **waddle_display** app is the TV dashboard: it rotates **screens** (slides), shows a bottom **ticker** marquee, renders scheduled **overlays**, and surfaces **alerts** from operators or adoption flows.

![Night-themed curator slide on a TV](../assets/screenshots/display/curator-night-1.png){ width="640" }

## Screens (slides)

Each screen row in SQLite defines:

- `screen_type` — built-in renderer (weather, news, photos, clocks, etc.)
- `config_json` — layout and content bindings
- Scheduling — dwell time, frequency weight, optional date windows

The **ScreenRotator** advances through the curated program. Slides preload media to reduce flicker between transitions.

See the [screen types catalog](../reference/screens.md) for all `screen_type` values and typical configuration.

## Ticker

The bottom marquee shows curated lines (time, weather, news headlines, quotes, stocks, static text, plugin content). Ticker text is held **in memory**; operators read the current snapshot via `GET /v1/ticker/items`.

Configure **ticker tapes** in the controller or via `GET/POST/PATCH /v1/ticker/tapes`. See [Ticker reference](../reference/ticker.md).

## Overlays

Overlays are full-screen or edge effects scheduled by calendar rules (birthdays, holidays, alarms). Examples: confetti, shape rain, matrix rain, photo slideshow, QR codes.

Operators manage rows via `GET/POST/PATCH/DELETE /v1/display/overlays`. Global kill-switch: `config_key_values` key `display.overlay.enabled` = `false`.

See [Overlay types](../reference/overlays.md).

## Alerts

REST `POST /v1/alerts` creates operator messages (optional QR payload). **Adoption** challenges also appear as security alerts on the display. Alerts respect priority and expiry.

## Window and kiosk behavior

On Linux release builds, startup window policy can maximize or run borderless depending on configuration. Pair with **disable screen blanking** on Pi (`xset`, DPMS, or Wayland equivalents).

## Data collection

A background **DataCollectionEngine** runs integrations sequentially. Each cycle fetches enabled providers and writes into SQLite + blob store. The curator refreshes after each cycle (and on configuration changes).

## Live preview

Operators with `navigation.control` can open a JPEG WebSocket stream (`POST /v1/display/live-preview/session` then `GET /v1/display/live-preview/ws?ticket=…`) for remote preview in the controller.

## Next steps

- [Controller guide](controller.md)
- [Integrations catalog](../reference/integrations.md)
- [Configuration](../reference/configuration.md)
