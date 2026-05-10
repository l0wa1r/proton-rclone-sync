# proton-rclone-sync
# Proton Drive Sync

Automated, event-driven backup for Proton Drive on Linux using `rclone` and `systemd`.

## Features
- **Efficient:** Only triggers on file changes (no cron needed).
- **Stable:** Optimized for Proton Drive API limits to prevent 422 errors.
- **Minimal:** Lightweight `oneshot` service.

## Installation

1. **Setup Units:**
   ```bash
   mkdir -p ~/.config/systemd/user/
   cp proton-sync.service proton-sync.path ~/.config/systemd/user/

2.  Configure Filter:
Create ~/.config/rclone/proton.filter to include/exclude files.

3. Enable:
systemctl --user daemon-reload
systemctl --user enable --now proton-sync.path

journalctl --user -u proton-sync.service -f
