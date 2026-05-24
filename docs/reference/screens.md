# Screen types

Screens are slides in the rotation program. Each row uses a `screen_type` and a `config_json` object validated against the type schema.

Fetch live schemas and examples:

```http
GET /v1/meta/config-schemas
GET /v1/meta/screen-types
```

Manage rows via `GET/POST/PATCH/DELETE /v1/screens`.

## Built-in screen types

| `screen_type` | Label | Typical use |
|---------------|-------|-------------|
| `static_text` | Static text | Custom message slide |
| `joke` | Joke | Setup/punchline from joke collectors |
| `quote` | Quote | Quoterism or configured quote source |
| `trivia` | Trivia | Multiple-choice trivia |
| `wifi` | Wi‑Fi | QR code for network join |
| `digital_clock` | Digital clock | Large time display |
| `analog_clock` | Analog clock | Analog face |
| `calendar_month` | Calendar month | Month grid |
| `photo_random` | Random photo | Random pick from photo catalog |
| `news` | News | RSS article layout |
| `news_columns` | News columns | Multi-column news |
| `news_stack` | News stack | Stacked headlines |
| `news_grid` | News grid | Grid of articles |
| `local_api` | Local API | Custom local HTTP payload |
| `admin_setup` | Admin setup | First-run / setup guidance |
| `controller_invite` | Controller invite | Pairing instructions on TV |
| `weather` | Weather | Current conditions |
| `photo` | Photo | Specific photo binding |
| `photo_collage` | Photo collage | Multi-photo layout |
| `video` | Video | Video playback (media_kit) |
| `stock_quotes` | Stock quotes | Symbol quotes |
| `task_board` | Task board | Trello or task integration |
| `home_assistant` | Home Assistant | Entity state dashboard |
| `data_health` | Data health | Integration status summary |
| `web_page` | Web page | Embedded WebView URL |
| `plugin_template` | Plugin template | Plugin-authored slide |
| `general_full_screen` | General full screen | OpenAI/KV multi-slot layout |
| `general_2_column` | General 2 column | Two-slot AI layout |
| `general_3_column` | General 3 column | Three-slot AI layout |
| `general_2x2` | General 2×2 | Four-slot grid |
| `general_3x2` | General 3×2 | Six-slot grid |

## Common configuration fields

Most types support scheduling and weighting on the screen row itself:

| Field | Purpose |
|-------|---------|
| `dwell_seconds` | Minimum time on slide |
| `frequency_weight` | Relative pick weight in curator |
| `enabled` | Include in program when true |
| `start_month` / `start_day` | Optional seasonal window |
| `data_key` | Bind to curated data slot |
| `require_news_photo` | News-family only: require RSS image for this slide (default true) |

Exact `config_json` keys are type-specific — use the schema from meta endpoints or the controller form.

## News grid (`news_grid`)

Fixed **3×2** grid of articles. Per cell: image, headline, and source by default.

| `config_json` key | Purpose |
|-------------------|---------|
| `showSummary` | When true, show truncated article body (uses `summaryCapacityCharsPerSlot`) |
| `qrMode` | `hidden`, `image_overlay_left`, or `image_overlay_right` — QR over the image edge |
| `feedId` / `categoryId` | Scope articles to one feed or content category |

Curator assigns slots `main_news_grid_0` … `main_news_grid_5`.

## Task board (`task_board`)

Kanban-style columns from synced task integrations (Trello today).

| `config_json` key | Purpose |
|-------------------|---------|
| `boardKey` | Board id (must match `boardIds` on the `tasks_trello` integration) |
| `maxTasksPerColumn` | Max cards per column (default **12**) |
| `showCompleted` | Include completed cards (default **false**) |

Requires a enabled **`tasks_trello`** integration with matching **`boardIds`**. Browse synced cards under **Data → Tasks**.

## Operator tips

- Use **data health** after enabling new integrations to verify collectors.
- **Controller invite** and **admin setup** help onboarding without SSH.
- **Web page** slides need WebKit/GTK on Linux; verify on target hardware.

## Next steps

- [Display runtime](../using/display.md)
- [Overlays](overlays.md)
- [REST API](../api/reference.md)
