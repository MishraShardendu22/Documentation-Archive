# Backup script Timer and Service

## Goal

Run a **GitHub backup script automatically every week**, even if the PC is **off**, and have it run **on next boot**.

---

## Final Working Architecture

* **systemd service** → runs the backup
* **systemd timer** → schedules it weekly
* **Compiled Go binary** → avoids runtime/SELinux issues
* **SELinux enforcing** → handled correctly (no disabling)

---

## Key Concepts (What is what)

### Daemon

* Long-running background process
* Your setup is **NOT** a daemon

### systemd service (oneshot)

* Runs → finishes → exits
* Used for jobs/scripts

### systemd timer

* Replaces cron
* Supports `Persistent=true`
* Knows system state (boot/offline)

### Why not cron

* Cron **misses runs** if system is off
* No state, poor logging, weak supervision

---

## Scheduling Requirement

* Run **every Monday at 00:40**
* If system is off → run **once on next boot**

Solved by:

```ini
OnCalendar=Mon *-*-* 00:40:00
Persistent=true
```

---

## Final Files (Canonical)

### `/etc/systemd/system/github-backup.service`

```ini
[Unit]
Description=GitHub Backup Script

[Service]
Type=oneshot
User=mishrashardendu22
WorkingDirectory=/home/mishrashardendu22/GitHub-BackUp-Script
Environment=HOME=/home/mishrashardendu22
ExecStart=/home/mishrashardendu22/GitHub-BackUp-Script/github-backup
TimeoutStartSec=infinity
```

---

### `/etc/systemd/system/github-backup.timer`

```ini
[Unit]
Description=Weekly GitHub Backup

[Timer]
OnCalendar=Mon *-*-* 00:40:00
Persistent=true

[Install]
WantedBy=timers.target
```

---

## Critical Lessons Learned

### 1. **Never use `go run` in systemd**

Reason:

* Needs Go toolchain
* Uses `$HOME/.cache`, `$GOMODCACHE`
* Triggers endless SELinux denials

Rule:

> **Build once. Run binary. Always.**

---

### 2. SELinux is mandatory on Fedora

* systemd runs in `init_t`
* Home files are `user_home_t`
* SELinux blocks access even with correct UNIX permissions

How it was diagnosed:

```bash
ausearch -m avc -ts recent
```

Example denial:

```
avc: denied { read } comm="go" tcontext=user_home_t
```

---

### 3. SELinux Fix (Correct Way)

Label the project as executable content:

```bash
sudo semanage fcontext -a -t bin_t "/home/mishrashardendu22/GitHub-BackUp-Script(/.*)?"
sudo restorecon -Rv /home/mishrashardendu22/GitHub-BackUp-Script
```

Verify:

```bash
ls -Z go.mod
# → object_r:bin_t
```

---

### 4. Build Once

```bash
cd ~/GitHub-BackUp-Script
go build -o github-backup
```

This removed **all remaining SELinux issues**.

---

## systemd Lifecycle Commands (Cheat Sheet)

Enable timer:

```bash
sudo systemctl enable --now github-backup.timer
```

Check next run:

```bash
systemctl list-timers github-backup.timer
```

Force run now:

```bash
sudo systemctl start github-backup.service
```

Logs:

```bash
journalctl -u github-backup.service -f
```

Reset failed state:

```bash
sudo systemctl reset-failed github-backup.service
```

---

## Status Meanings (Important)

* `activating (start)`
  → **Job is still running** (normal for long tasks)

* `inactive (dead)`
  → Finished successfully

* `failed (signal)`
  → You manually stopped it (`systemctl stop`)

* `failed (exit-code)`
  → Real error (check logs)

---

## Why It Now Works (Guaranteed)

* Binary execution → no Go runtime / cache access
* Correct SELinux labels
* Timer enabled + persistent
* systemd handles missed runs

**Yes — it will run next week.**

---

## Final Rule to Remember

> Interactive commands ≠ automated services
> Fedora + systemd + SELinux requires **explicit intent**

Once configured correctly, it is **rock solid**.
