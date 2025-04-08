# macbookair-t2-archlinux
## Quick Guide: Fix Suspend-to-RAM on MacBook Air 2018 (T2 Chip) with EndeavourOS (Arch Linux)

**Date Written:** April 7, 2025  
**Purpose:** Fixes the issue where a MacBook Air 2018 with a T2 chip on EndeavourOS (Arch Linux) with a T2 kernel wakes up or reboots or fails after suspend-to-RAM. The fix uses a systemd service to stop `t2fanrd` and unload the `apple-bce` module before suspend, then restore them after resume.

---

## Steps

**Create systemd service:**
   ```
   sudo nano /etc/systemd/system/suspend-fix-t2.service
   ```
**Paste:**
```
[Unit]
Description=Fix suspend for T2 Macs
Before=sleep.target
StopWhenUnneeded=yes

[Service]
Type=oneshot
RemainAfterExit=yes
ExecStart=/usr/bin/systemctl stop t2fanrd.service ; /usr/bin/rmmod --force apple-bce
ExecStop=/usr/bin/modprobe apple-bce ; /usr/bin/systemctl start t2fanrd.service

[Install]
WantedBy=sleep.target
```
Save (Ctrl+O, Enter, Ctrl+X).

**Reload and enable service:**
```
sudo systemctl daemon-reload
sudo systemctl enable suspend-fix-t2.service
```
**Test suspend:**
```
sudo systemctl suspend
```
Verify the system stays suspended and resumes correctly.
