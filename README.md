# proton-rclone-sync

Automated, event-driven backup for Proton Drive on Linux using `rclone` and `systemd`.

## Features

- **Efficient:** Only triggers on file changes — no cron needed.
- **Stable:** Optimized for Proton Drive API limits to prevent 422 errors.
- **Minimal:** Lightweight `oneshot` service with no background processes.

## Requirements

- [`rclone`](https://rclone.org/install/) installed and available in `$PATH`
- A Proton Drive remote configured via `rclone config` (see [rclone Proton Drive docs](https://rclone.org/protondrive/))

## Installation

1. **Copy the unit files:**

   ```bash
   mkdir -p ~/.config/systemd/user/
   cp proton-sync.service proton-sync.path ~/.config/systemd/user/
   ```

2. **Configure the filter file:**

   Copy the example filter and edit it to match your folder structure:

   ```bash
   mkdir -p ~/.config/rclone/
   cp proton.filter ~/.config/rclone/proton.filter
   ```

3. **Enable and start:**

   ```bash
   systemctl --user daemon-reload
   systemctl --user enable --now proton-sync.path
   ```

## Usage

The path watcher monitors your home directory and automatically triggers a sync when files change. To monitor logs in real time:

```bash
journalctl --user -u proton-sync.service -f
```

To trigger a manual sync:

```bash
systemctl --user start proton-sync.service
```

## Filter File

Edit `~/.config/rclone/proton.filter` to control which folders are synced:

```
+ /Documents/**
+ /Pictures/**
+ /Videos/**
- /Downloads/**
- /.*
- *
```

Rules are processed top to bottom. A `+` includes the path, `-` excludes it. The final `- *` excludes everything not explicitly included.

## Troubleshooting

**422 errors from Proton Drive:** The service is already tuned for this (`--transfers 1`, `--tpslimit 1`), but if errors persist, try increasing `--drive-pacer-min-sleep` in `proton-sync.service`.

**Sync not triggering:** Make sure the path watcher is active:

```bash
systemctl --user status proton-sync.path
```
