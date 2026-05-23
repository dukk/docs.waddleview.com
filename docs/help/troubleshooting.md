# Troubleshooting

## Windows development

| Symptom | Fix |
|---------|-----|
| `Building with plugins requires symlink support` | Enable **Developer Mode**, new terminal, `flutter clean`, rebuild |
| `atlstr.h` missing | Install **C++ ATL** in Visual Studio Installer |
| `WebView2Loader.dll` locked | Stop running `waddle_display` (Task Manager if needed) |
| `advisoriesUpdated` decode warning during `pub get` | Often harmless; run `flutter upgrade` if `pub get` aborts |

## Linux / Raspberry Pi

| Symptom | Fix |
|---------|-----|
| `libmpv.so.2` not found | `sudo apt install libmpv2 mpv` or `WADDLE_INSTALL_RUNTIME_PACKAGES=1` on install |
| GTK / WebKit errors | Install `libgtk-3-0`, `libwebkit2gtk-4.1-dev` (build) or runtime packages from `runtime-apt-packages.txt` |
| OAuth sign-in fails | Ensure D-Bus + **Secret Service** (gnome-keyring) for Google/Microsoft tokens |
| Blank screen / DPMS | Disable screen blanking (`xset`, compositor settings) |

## Display REST / TLS

| Symptom | Fix |
|---------|-----|
| Browser rejects HTTPS | Accept self-signed cert once, or use controller BFF proxy |
| CORS errors from controller | Complete adoption (stores Origin) or set `WADDLE_DISPLAY_HTTP_CORS_ORIGINS` |
| `401 unauthorized` | Re-adopt; check Bearer prefix and key rotation |
| `503 adoption_unavailable` | Missing `waddle_instance.id` — first launch or broken install |

## Controller / BFF

| Symptom | Fix |
|---------|-----|
| `502 display_timeout` | Raise `WADDLE_CONTROLLER_PROXY_UPSTREAM_TIMEOUT_MS` on slow Pi |
| Proxy cannot reach display | Verify LAN URL, firewall on 8787, TLS vs HTTP mismatch |

## Blobs and media

| Symptom | Fix |
|---------|-----|
| Broken slide images | Check `GET /v1/media/blob-by-key`; verify `media/` on disk after restore |
| Missing RSS images | Re-run collector; confirm feed returns image URLs |

## Process restarts

Release builds **restart the process** on fatal errors (systemd-friendly). **Layout overflow** assertions are logged as recoverable and do **not** restart.

Check `debug_console_logs/` under app support in debug builds.

## Data and upgrades

- Back up SQLite before major version jumps (Drift migrations run at startup).
- After restore, restart `waddle_display` so caches reload.

## Getting help

- [GitHub issues](https://github.com/dukk/waddle-view/issues)
- [Product site](https://waddleview.com/)
