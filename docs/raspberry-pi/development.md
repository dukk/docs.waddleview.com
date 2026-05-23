# Pi and Linux development

## Monorepo layout

| Path | Purpose |
|------|---------|
| `apps/waddle_display/` | Application root |
| `docs/pi/` | Legacy Pi docs (migrated here) |
| `deploy/linux-arm64/` | Install templates, upgrade scripts |

## ARM64 release builds (CI)

The [`release-pi.yml`](https://github.com/dukk/waddle-view/blob/main/.github/workflows/release-pi.yml) workflow builds on **`ubuntu-22.04-arm`** inside **`debian:bookworm-slim`** so binaries match **Raspberry Pi OS Bookworm** (glibc ≤ 2.36, correct `libmpv.so.2` SONAME). Do not move to Ubuntu 24.04 without revisiting the glibc check.

## Local development

| Platform | Command |
|----------|---------|
| Windows | `flutter run -d windows` |
| Linux / Pi | `flutter run -d linux` |

From `apps/waddle_display/`:

```bash
flutter pub get
flutter analyze
flutter test --coverage
dart run tool/coverage_check.dart --min=85 --target=90
```

After schema edits, from repo root:

```bash
cd packages/waddle_shared
dart run build_runner build --delete-conflicting-outputs
flutter test
```

## Secret storage on Linux

`flutter_secure_storage` needs **D-Bus** and **Secret Service** (e.g. gnome-keyring) for Google/Microsoft OAuth. Minimal images may lack this — plan a deployment fallback; do not store OAuth tokens in SQLite.

**Static API keys** use environment variables — see [`apps/waddle_display/.env.example`](https://github.com/dukk/waddle-view/blob/main/apps/waddle_display/.env.example).

## OpenAI / provider keys

| Item | Value |
|------|--------|
| Provider id | e.g. `joke_openai` |
| Env var | `WADDLE_DISPLAY_OPENAI_API_KEY` (and related names per provider) |
| Debug | `.env` in `apps/waddle_display/` (gitignored) |
| Production | systemd `Environment=` |

## Agent / contributor rules

See [`AGENTS.md`](https://github.com/dukk/waddle-view/blob/main/AGENTS.md) and [Contributing](../help/contributing.md).

## Next steps

- [Development setup](../getting-started/dev-setup.md)
- [Install on Pi](install.md)
