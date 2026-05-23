# Development setup

Waddle View is a Pub **workspace** monorepo. Resolve dependencies once from the repository root, generate Drift code in `waddle_shared`, then run the display app.

## Prerequisites

- **Flutter** (stable), `flutter doctor` clean for your target
- **Windows**: Visual Studio 2022 with **Desktop development with C++** and **C++ ATL**; **Developer Mode** for symlink support
- **Linux**: GTK/WebKit packages per [Flutter Linux desktop](https://docs.flutter.dev/platform-integration/linux/setup)

See the [display app README](https://github.com/dukk/waddle-view/blob/main/apps/waddle_display/README.md) for full prerequisite tables.

## First-time setup

From the **waddle-view** repository root:

```bash
flutter pub get
dart run apps/waddle_display/tool/generate_about_manifest.dart
cd packages/waddle_shared
dart run build_runner build --delete-conflicting-outputs
flutter test
cd ../..
```

After editing Drift schema (`packages/waddle_shared/lib/persistence/`), re-run `build_runner` in that package.

## Run the display

```bash
flutter devices
flutter run -d windows    # Windows dev
flutter run -d linux      # Linux desktop or Pi with toolchain
```

| Mode | Command |
|------|---------|
| Debug (default) | `flutter run` |
| Profile | `flutter run --profile` |
| Release | `flutter run --release` |

## API keys for development

Copy [`apps/waddle_display/.env.example`](https://github.com/dukk/waddle-view/blob/main/apps/waddle_display/.env.example) to `.env` in `apps/waddle_display/`. Set `WADDLE_DISPLAY_*` keys for providers you enable (OpenAI, OpenWeather, Pexels, etc.). The file is gitignored; **release builds do not read `.env`** — use systemd `Environment=` on Pi.

## Run the controller (optional)

From `apps/waddle_controller/`:

```bash
npm ci
npm run dev
```

Vite serves the SPA at **https://127.0.0.1:5173**; the BFF listens at **https://127.0.0.1:5199** and proxies `/bff` to displays. Set `WADDLE_DISPLAY_HTTP_CORS_ORIGINS` to include your controller origin if pairing from the browser.

## Quality checks

```bash
python scripts/waddle_checks.py fast   # inner loop
python scripts/waddle_checks.py full     # CI parity before merge
```

## Next steps

- [Architecture](architecture.md)
- [Configuration reference](../reference/configuration.md)
- [Troubleshooting](../help/troubleshooting.md)
