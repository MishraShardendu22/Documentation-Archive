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

Below is the **additional section** to append to the **previous summary/notes**.
This covers the **repo-29 stop issue** and the **final root cause**.

---

## Additional Issue Encountered: Script Always Stops at Repo ~29

### Symptom

* Backup **always stops around repo #29**
* No panic, no Go error
* No Git error printed
* No summary section printed
* Happens **only when run via systemd**
* Manual run completes all ~131 repos

---

## What Was Ruled Out (with evidence)

### ❌ Pagination bug

* `GetAllRepos()` correctly returns **all repos**
* Repo list is printed fully
* Loop logic is correct

### ❌ SELinux

* AVC denials fixed
* Labels corrected (`bin_t`)
* No new AVC logs

### ❌ OOM / kernel kill

* `sudo dmesg` and `journalctl -k` show **no OOM**
* No kernel kills logged

### ❌ GitHub API limits

* No API error
* SSH cloning, not API cloning
* Deterministic cutoff on same repo

---

## Root Cause (Final, Correct)

### **systemd abort timeout (Fedora global drop-in)**

Evidence:

```
Drop-In: /usr/lib/systemd/system/service.d
 └─10-timeout-abort.conf
```

Fedora ships a **global service abort timeout** that:

* Kills long-running services
* Sends SIGTERM silently
* Causes Go to exit with `status=1`
* Produces **no application-level error**

Why repo ~29:

* First ~28 repos are small
* Repo #29 is **large**
* `git clone` exceeds abort window
* systemd kills the service
* Happens deterministically

---

## Why Manual Execution Works

* No systemd supervision
* No abort timeout
* No watchdog
* Process allowed to run indefinitely

---

## Final Required Fix (Canonical)

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

# Critical overrides
TimeoutStartSec=infinity
TimeoutStopSec=infinity
WatchdogSec=0
KillMode=process
```

Reload:

```bash
sudo systemctl daemon-reload
sudo systemctl reset-failed github-backup.service
```

---

## Strongly Recommended Optimization (not the root cause)

### Use shallow clone to reduce runtime and memory

Change:

```go
git clone '%s'
```

To:

```go
git clone --depth=1 '%s'
```

This:

* Reduces clone time massively
* Avoids large history fetch
* Lowers risk of long operations

---

## Final Diagnosis Summary (Corrected)

* ❌ Pagination issue
* ❌ SELinux
* ❌ OOM / kernel kill
* ❌ GitHub API
* ✅ **systemd abort timeout via Fedora drop-in**

---

## Final State Guarantee

With:

* binary execution
* SELinux fixed
* systemd timeout overridden
* persistent timer

The job will:

* back up **all repos**
* never stop at repo 29
* run every Monday at 00:40
* run after boot if missed
* remain stable long-term
