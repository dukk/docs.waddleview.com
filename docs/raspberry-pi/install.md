# Install on Raspberry Pi

Production installs use pre-built **Linux ARM64** release tarballs from [GitHub Releases](https://github.com/dukk/waddle-view/releases).

## Tier 0: one-line install

On 64-bit Raspberry Pi OS (Bookworm-aligned), matching **arm64** or **x64** assets:

```bash
curl -fsSL https://raw.githubusercontent.com/dukk/waddle-view/main/deploy/install-latest-release.sh | bash
```

Non-interactive upgrade (skip confirmation when replacing `/opt/waddle-view/bundle`):

```bash
curl -fsSL https://raw.githubusercontent.com/dukk/waddle-view/main/deploy/install-latest-release.sh | bash -s -- --yes
```

Install runtime packages when `ldd` reports missing libraries:

```bash
WADDLE_INSTALL_RUNTIME_PACKAGES=1 curl -fsSL https://raw.githubusercontent.com/dukk/waddle-view/main/deploy/install-latest-release.sh | bash
```

Pin the script URL to a release tag or commit SHA if you do not want `main` to move.

## Tier 1: manual tarball

1. Download `waddle-view-linux-arm64-<tag>.tar.gz` from Releases (verify SHA256 when published).
2. On the Pi:

```bash
tar xzf waddle-view-linux-arm64-v1.0.0.tar.gz
cd waddle-view-linux-arm64-v1.0.0
sudo bash install.sh
```

3. On first launch, the app creates **`waddle_instance.id`** in app support (bootstrap for adoption).
4. Install system libraries if needed:

```bash
sudo apt update && sudo apt install -y --no-install-recommends \
  at-spi2-core libmpv2 mpv libgtk-3-0 libsecret-1-0
```

Or re-run `install.sh` with `WADDLE_INSTALL_RUNTIME_PACKAGES=1`.

5. Configure **autostart** (`~/.config/autostart/*.desktop`) or install sample **`waddle-view.service`** (edit `User`, `DISPLAY`, paths).
6. **Disable screen blanking** (`xset s off`, `xset -dpms`, or Wayland equivalents).

## Tier 2: flashable SD image

For a ready-to-flash `.img`, see [deploy/pi-image](https://github.com/dukk/waddle-view/tree/main/deploy/pi-image) — requires a pre-built ARM64 bundle. Follow Raspberry Pi trademark guidance if redistributing images.

## Data locations

- **SQLite** and **`media/`** live under the Flutter app support directory for the user running the app (`path_provider` / XDG on Linux).
- **Instance id** (packaged): `/etc/waddle-view/instance.id`

## Environment variables

Set provider API keys and bind options via systemd `Environment=` — see [Configuration reference](../reference/configuration.md). Release builds **do not** read `.env` files.

## Next steps

- [Upgrade](upgrade.md)
- [Pi development](development.md)
- [Controller pairing](../using/controller.md)
