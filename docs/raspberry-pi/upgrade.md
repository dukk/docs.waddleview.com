# Upgrade on Raspberry Pi

## Remote upgrade from your dev machine

The repo includes [`deploy/pi-remote-upgrade.py`](https://github.com/dukk/waddle-view/blob/main/deploy/pi-remote-upgrade.py) (Python 3.9+, stdlib). It compares versions, backs up `/opt/waddle-view/bundle` to `bundle.backup.<timestamp>`, then runs `install.sh`.

**Authentication**

- SSH: key-based auth recommended (`BatchMode=yes` by default; use `--no-batch` for password auth)
- GitHub: `GITHUB_TOKEN` or `GH_TOKEN` required for Actions artifacts (`actions:read`)

**Examples**

```bash
# Local tarball
python3 deploy/pi-remote-upgrade.py pi@raspberrypi.local \
  --bundle ./waddle-view-linux-arm64-v1.0.0.tar.gz

# Latest release or Actions artifact
export GITHUB_TOKEN=ghp_...
python3 deploy/pi-remote-upgrade.py --ssh pi@raspberrypi.local --source auto

# Non-interactive
python3 deploy/pi-remote-upgrade.py pi@raspberrypi.local \
  --bundle ./waddle-view-linux-arm64-v1.0.0.tar.gz --yes
```

Flags: `-i` / `--identity`, `-p` / `--port`, `--branch`, `--dry-run`, `--yes`.

## Manual upgrade on the Pi

1. Stop the app (`systemctl --user stop waddle-view` or end the session).
2. Replace **`bundle/`** under `/opt/waddle-view` with the new release. **Preserve** SQLite and app support (including `waddle_instance.id`) unless resetting auth.
3. Start the app again.
4. **Drift** runs migrations on startup — back up the SQLite file before major upgrades.

## In-band upgrade from the controller

When `GET /v1/health` returns `upgrade_capable: true`:

1. Copy [`waddle-view-upgrade.sh`](https://github.com/dukk/waddle-view/blob/main/deploy/linux-arm64/waddle-view-upgrade.sh) to the Pi (e.g. `/opt/waddle-view/waddle-view-upgrade.sh`) and `chmod +x`.
2. Set **`WADDLE_DISPLAY_UPGRADE_SCRIPT`** to that path.
3. Grant passwordless sudo for the script only:

```sudoers
pi ALL=(root) NOPASSWD: /opt/waddle-view/waddle-view-upgrade.sh
```

The controller **Backup & restore** tab can then call `POST /v1/display/ops/upgrade` with a release download URL.

Without sudo, the UI still links to GitHub Releases for manual upgrades.

## Operator session rotation

After upgrade, rotate passwords via controller **Settings → Users** or `POST /v1/users/<id>/password`. `POST /v1/auth/logout` invalidates the current session.

## Next steps

- [Install](install.md)
- [Troubleshooting](../help/troubleshooting.md)
