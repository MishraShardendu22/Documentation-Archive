# Linux Filesystem Hierarchy (FSH) — Practical Documentation

---

## `/` — Root Directory

The top-level directory of the entire filesystem.

* Every file and directory originates from `/`
* All other directories exist inside `/`

Example:

```
/bin
/etc
/home
/usr
```

If someone says "look in slash", they mean `/`.

---

## `/root` — Root User Home

Home directory of the **root (superuser)**.

* Stores root's personal configuration
* Root-only scripts and files
* Independent from `/home`

Example:

```
/root/.bashrc
/root/.ssh/
```

---

## `/bin` — User Binaries

Essential command binaries available for all users.

Contains programs required for:

* Basic system operation
* Single-user mode
* Everyday commands

Examples:

```
ls
cp
mv
cat
bash
```

---

## `/sbin` — System Binaries

Essential binaries for **system administration**.

Typically used by root.

Examples:

```
reboot
fdisk
iptables
mkfs
swapon
```

---

## `/dev` — Device Files

Special files representing hardware and virtual devices.

Programs interact with hardware through these files.

Examples:

```
/dev/sda
/dev/sda1
/dev/tty1
/dev/null
/dev/random
```

Not real files — kernel interfaces.

---

## `/var` — Variable Data

Files that change frequently.

Used for logs, caches, spools, databases.

Subdirectories:

```
/var/log   → system logs
/var/lib   → application state data
/var/mail  → mail storage
/var/tmp   → temp files kept across reboots
```

---

## `/mnt` — Temporary Mount Point

Used for **manual temporary mounts**.

Example:

```
mount /dev/sdb1 /mnt
```

Admin-controlled.

---

## `/media` — Removable Media Mounts

Automatic mount point for removable devices.

Examples:

```
/media/user/USB
/media/user/SDCARD
```

Created when device is inserted.

---

## `/usr` — User Programs & Libraries

Contains most user-space software.

Includes:

```
/usr/bin
/usr/sbin
/usr/lib
/usr/share
```

Think of `/usr` as: *main application storage*.

---

## `/etc` — Configuration Files

System-wide configuration.

Contains text files controlling:

* OS behavior
* Services
* Applications

Examples:

```
/etc/passwd
/etc/fstab
/etc/ssh/sshd_config
```

No binaries here.

---

## `/boot` — Boot Files

Files required during system startup.

Contains:

* Kernel images
* Initramfs
* Bootloader files

Examples:

```
vmlinuz
initrd.img
grub/
```

---

## `/opt` — Optional Software

Third-party application installs.

Usually self-contained directories.

Example:

```
/opt/google/
/opt/oracle/
```

Binaries may be symlinked into `/bin`.

---

## `/home` — User Home Directories

Contains home folders for normal users.

Example:

```
/home/alice
/home/bob
```

Each user stores personal files here.

---

## `/tmp` — Temporary Files

Short-lived temporary data.

* Used by programs
* Often cleared on reboot

Anyone can write here.

---

# Quick Mental Map

```
/bin   → essential commands
/sbin  → admin commands
/etc   → configs
/dev   → devices
/home  → users
/root  → root user
/usr   → applications
/var   → logs & changing data
/tmp   → temp files
/mnt   → manual mounts
/media → removable devices
/boot  → boot files
/opt   → third-party apps
```

---
