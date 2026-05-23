# Integrations

Integrations are **data collectors** registered in SQLite (`integrations.integration_type`). Each enabled integration runs on a schedule in the **DataCollectionEngine** and writes catalog rows (jokes, RSS, photos, weather, calendar events, etc.).

Configure via the controller **Integrations** tab or `GET/PATCH /v1/integrations/{id}`.

![Integrations in the controller](../assets/screenshots/controller/integrations-1.png){ width="640" }

## Schema discovery

```http
GET /v1/meta/config-schemas
```

Each `integration_types` entry includes `label`, `config_json_schema`, `example_config_json`, and `requires_accounts`.

## Built-in integration types

| `integration_type` | Family | Typical secrets / accounts |
|--------------------|--------|----------------------------|
| `news_rss` | news | RSS feed URLs in interests |
| `news_twitter` | news | API credentials in config |
| `news_facebook` | news | API credentials |
| `news_linkedin` | news | API credentials |
| `joke_openai` | joke | `WADDLE_DISPLAY_OPENAI_API_KEY` |
| `joke_jokeapi` | joke | JokeAPI key in env/config |
| `trivia_openai` | trivia | OpenAI API key |
| `trivia_opentdb` | trivia | Open TDB (env optional) |
| `quote_quoterism` | quote | Quoterism endpoint config |
| `weather_openweathermap` | weather | `WADDLE_DISPLAY_OPEN_WEATHER_MAP_API_KEY` |
| `weather_openmeteo` | weather | Usually no key |
| `weather_alerts_nws` | weather | US NWS alerts |
| `air_quality_openmeteo` | weather | Open-Meteo air quality |
| `stock_finnhub` | stock | `WADDLE_DISPLAY_FINHUB_API_KEY` |
| `photo_pexels` | photo | Pexels API key |
| `video_pexels` | video | Pexels API key |
| `photo_bing_iotd` | photo | Bing image of the day |
| `photo_flickr` | photo | Flickr API key |
| `photo_nasa_apod` | photo | NASA APOD (DEMO_KEY or API key) |
| `photo_nasa_mars_rover` | photo | NASA API key |
| `photo_nasa_earth_imagery` | photo | NASA API key |
| `photo_google` | photo | Google OAuth (SecretStore) |
| `video_google` | video | Google OAuth |
| `photo_onedrive` | photo | Microsoft Graph OAuth |
| `video_onedrive` | video | Microsoft Graph OAuth |
| `calendar_google` | calendar | Google OAuth |
| `calendar_outlook` | calendar | Microsoft Graph OAuth |
| `calendar_ical` | calendar | ICS URL in config |
| `calendar_mealviewer` | calendar | Mealviewer district URL |
| `tasks_trello` | tasks | Trello API token |
| `home_assistant` | iot | HA URL + long-lived token |
| `general_openai` | general | OpenAI for multi-slot layouts |
| `plugin_http` | plugin | Sidecar `POST /collect` |

Static API keys are read from **environment variables** at startup — not from SQLite. OAuth tokens use **SecretStore** / encrypted integration secrets configured in the UI.

## Interests tables

Operator-facing feeds, locations, and symbol lists live under `/v1/interests/*` (weather locations, RSS feeds, stock symbols, joke/trivia categories). Collectors read these when syncing.

## Listing and filters

```http
GET /v1/integrations?enabled=true&limit=25&offset=0
```

Paginated mode adds `total`, `limit`, `offset`. Filters: `family`, `integration_type`, `q`, `secrets_configured`, `accounts_configured`, `facets=family`.

## Plugin collectors

HTTP plugins expose `POST /collect` and register as `plugin_http`. See [Plugins](plugins.md).

## Next steps

- [Configuration](configuration.md)
- [Ticker](ticker.md)
- [REST API reference](../api/reference.md)
