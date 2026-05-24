# Display runtime

The **waddle_display** app is the TV dashboard: it rotates **screens** (slides), shows a bottom **ticker** marquee, renders scheduled **overlays**, and surfaces **alerts** from operators or adoption flows.

![Night-themed curator slide on a TV](../assets/screenshots/display/curator-night-1.png){ width="640" }

## Screens (slides)

Each screen row in the database defines:

- `screen_type` — built-in renderer (weather, news, photos, clocks, task board, etc.)
- `config_json` — layout and content bindings
- Scheduling — dwell time, frequency weight, optional date windows
- **`require_news_photo`** (news-family types) — when true, only RSS rows with a downloaded image are used for that slide; curator may fall back to a newspaper icon when placement rules require a row without a photo

The **ScreenRotator** advances through the curated program. Slides preload media to reduce flicker between transitions.

### Task board (`task_board`)

Shows Trello (or future task) columns for a **`boardKey`** matching the integration’s **`boardIds`**. Optional **`maxTasksPerColumn`** (default 12) and **`showCompleted`** (default false).

See the [screen types catalog](../reference/screens.md) for all `screen_type` values.

## Ticker

The bottom marquee shows curated lines (time, weather, news headlines, quotes, stocks, static text, plugin content). Ticker text is held **in memory**; operators read the current snapshot via `GET /v1/ticker/items`.

Tape **`dateOrder`** and **`timeFormatPreset`** override display defaults (`controller_date_order`, `controller_time_format`) when set. Weather temperatures follow **`display.weather.temperature_unit`** (`c` or `f`; collectors store °C).

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

A background **DataCollectionEngine** runs integrations sequentially (unless **SaaS feed mode** is enabled). Each cycle fetches enabled providers and writes into the database + blob store. The curator refreshes after each cycle (and on configuration changes).

## Live preview

When enabled (`WADDLE_DISPLAY_LIVE_PREVIEW_ENABLED`, default on), operators with `navigation.control` can:

1. `GET /v1/display/live-preview` — status (`configured`, `enabled`, `fps`, `width`, `quality`)
2. `POST /v1/display/live-preview/session` — short-lived ticket
3. `GET /v1/display/live-preview/ws?ticket=…` with Bearer API key — binary JPEG frames (4-byte length prefix + JPEG)

On Linux, **`gst-launch-1.0`** is required for window capture. The controller **Remote** page proxies the WebSocket through the BFF and supports **Save frame** and a **`/remote/view`** pop-out. See [Controller guide](controller.md).

## Next steps

- [Controller guide](controller.md)
- [Integrations catalog](../reference/integrations.md)
- [Configuration](../reference/configuration.md)
