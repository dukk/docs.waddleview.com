# waddlectl CLI

**waddlectl** is a Dart CLI shipped beside release bundles. It operates on the same SQLite database and `media/` tree as **waddle_display**.

Prebuilt binaries:

- **Windows**: `waddlectl/bin/waddlectl.exe` inside the release zip
- **Linux**: `bundle/waddlectl/bin/waddlectl` next to `waddle_display` in the tarball

From a dev checkout:

```bash
cd apps/waddlectl
dart build cli -o build/waddlectl_release
```

## Backup create

```bash
dart run waddlectl --database=/path/to/waddle_display.db backup create
```

Creates a timestamped **`.zip`** or **`.tar.gz`** (current directory or `--output`).

| Flag | Purpose |
|------|---------|
| `--format=zip` / `tgz` | Archive format |
| `--no-include-database` | Omit SQLite file |
| `--no-include-blobs` | Omit `media/` tree |

The tool checkpoints WAL before copying the database. Archive layout:

```
manifest.json
db/waddle_display.db
media/...
```

## Backup restore

```bash
dart run waddlectl --database=/path/to/waddle_display.db backup restore --file backup.zip
```

Interactive confirmation (type `yes`) unless `--yes`. Replaces the database and media components present in the archive.

!!! warning
    Restore overwrites live data. Stop `waddle_display` first on production systems.

## Backup schedule

```bash
dart run waddlectl --database=/path/to/waddle_display.db backup schedule
```

Prints example **crontab** and **systemd user** unit snippets — does not install them.

## SQL seed export

```bash
dart run waddlectl --database=/path/to/waddle_display.db sqlite export-seed
```

Writes DELETE + INSERT statements for user tables. Use only on databases at the current schema version. Do not commit exports containing secrets.

## Migrate SQLite to PostgreSQL

```bash
dart run waddlectl --database=/path/to/waddle_display.db db migrate-to-postgres --to "$WADDLE_DISPLAY_DATABASE_URL"
```

| Flag | Purpose |
|------|---------|
| `--to` | Target Postgres URL (default: `WADDLE_DISPLAY_DATABASE_URL` env) |
| `--dry-run` | Compare row counts only; no writes |
| `--force` | Overwrite a non-empty Postgres target |

Stop **waddle_display** before migrating. After migration, set **`WADDLE_DISPLAY_DATABASE_URL`** in systemd and use **`pg_dump`** for backups instead of SQLite archive routes.

For the controller BFF database, use `npm run db:migrate-to-postgres` in `apps/waddle_controller` (see [controller README](https://github.com/dukk/waddle-view/blob/main/apps/waddle_controller/README.md)).

## REST and controller parity

Admins can run the same backup/restore flow via:

- `POST /v1/display/backup/jobs` (display API)
- Controller **Backup & restore** (proxied through BFF)

See [REST API reference](../api/reference.md) and [Controller guide](../using/controller.md).

## Next steps

- [Pi install](../raspberry-pi/install.md)
- [Troubleshooting](../help/troubleshooting.md)
