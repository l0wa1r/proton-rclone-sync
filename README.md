# proton-rclone-sync

Automated, event-driven backup for Proton Drive on Linux using `rclone` and `systemd`.

## Features

- **Efficient:** Only triggers on file changes — no cron needed.
- **Minimal:** Lightweight `oneshot` service with no background processes.
- **Smart:** Reads Proton Drive before uploading — only sends new or modified files.
- **One-Way:** One-way sync (local → Proton only).

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

**422 errors from Proton Drive:** Usually caused by a corrupted draft stuck on Proton's side. Find the failing file in the logs and delete it from Proton Drive via web, then retry. The service is tuned to minimize these (`--transfers 1`, `--tpslimit 1`, `--protondrive-replace-existing-draft=true`).

**Sync not triggering:** Make sure the path watcher is active:

```bash
systemctl --user status proton-sync.path
```
