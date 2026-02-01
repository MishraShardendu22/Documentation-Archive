**Why was Linux needed?**
Linux was created to provide a free, open-source, and customizable alternative to proprietary operating systems. Existing systems were expensive, closed-source, and restrictive. Linux allows users to study, modify, and distribute the source code, making it ideal for servers, developers, and large-scale systems.
ctrl + alt + f3
---

**What is Linux?**
Linux is an open-source, Unix-like operating system kernel used as the core of many operating systems (distributions) such as Ubuntu, Debian, Fedora, Arch, etc. It manages hardware, processes, memory, filesystems, and provides an interface for users and applications.

---

**What is a File System in Linux?**
A file system is the method Linux uses to store, organize, and manage files on storage devices. Everything in Linux is treated as a file (including devices and processes). Files are organized in a single hierarchical tree starting from the root directory `/`.

---

**Commands in Linux (with purpose)**

```
# First-10

ls        - List directory contents
cd        - Change directory
pwd       - Show current directory path
cp        - Copy files/directories
mv        - Move or rename files
rm        - Delete files/directories
mkdir     - Create directory
rmdir     - Delete empty directory
touch     - Create empty file / update timestamp
cat       - Display file content
```

---

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

If someone says “look in slash”, they mean `/`.

---

## `/root` — Root User Home

Home directory of the **root (superuser)**.

* Stores root’s personal configuration
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

Below are **structured, deep, system-level notes** on ZIP, GZIP, TAR, and TAR+GZIP, including internals, behavior, tradeoffs, and real-world usage.

---

# 1. Fundamental Concepts

## Compression

Reduces size of data using algorithms.

Examples:

* gzip
* bzip2
* xz
* zstd

Compression does **not** bundle multiple files.

---

## Archiving

Bundles multiple files/directories into a single stream.

Examples:

* tar
* cpio

Archiving does **not** compress.

---

## Archive + Compression

Two-stage pipeline:

```
FILES → ARCHIVE → COMPRESS
```

or

```
FILES → tar → gzip → archive.tar.gz
```

---

# 2. gzip

## Purpose

Single-file compressor.

## Characteristics

* Input: one file
* Output: one file.gz
* No directory awareness

Example:

```
gzip data.txt
→ data.txt.gz
```

To restore:

```
gunzip data.txt.gz
```

or

```
gzip -d data.txt.gz
```

---

## What gzip Stores

Inside gzip stream:

* Compressed data
* CRC32 checksum
* Original size
* Original filename (optional)

Does NOT store:

* Permissions
* Owner
* Group
* Symlink info
* Directory structure

---

## gzip Algorithm

Uses DEFLATE:

* LZ77 sliding window
* Huffman coding

Optimized for:

* Speed
* Streaming

---

## gzip Strengths

* Fast
* Simple
* Ubiquitous
* Stream-friendly

---

## gzip Weaknesses

* One file only
* Metadata loss
* Cannot recreate directory trees

---

# 3. zip

## Purpose

Combined archiver + compressor.

## Characteristics

* Bundles many files
* Each file compressed separately
* Central directory at end

Example:

```
zip -r project.zip project/
```

---

## ZIP Internal Structure

```
[file1 header][file1 data]
[file2 header][file2 data]
...
[central directory]
```

Central directory stores:

* File names
* Offsets
* Compression method
* Metadata

---

## ZIP Compression

Usually DEFLATE per-file.

Important consequence:

* Cannot stream easily
* Must seek to end for directory

---

## ZIP Metadata Support

Stores:

* Filenames
* Timestamps
* Optional permissions

Weak or inconsistent:

* Unix permissions
* Ownership
* Symlinks
* Device files

Result:
Linux restores may break.

---

## ZIP Strengths

* Cross-platform
* Random access
* Single-file extraction

---

## ZIP Weaknesses

* Metadata loss
* Poor streaming
* Not ideal for system backups

---

# 4. tar

## Purpose

Pure archiver.

## Characteristics

* Sequential stream
* No compression

Example:

```
tar -cf archive.tar folder/
```

---

## TAR Internal Model

```
[header][file data][padding]
[header][file data][padding]
...
```

Header stores:

* Path
* Mode (permissions)
* UID/GID
* Owner/group
* Type flag
* Symlink target
* Size
* mtime

Everything Linux cares about.

---

## tar Strengths

* Perfect metadata preservation
* Streaming-friendly
* Predictable format

---

## tar Weaknesses

* Large size without compression

---

# 5. tar + gzip

Pipeline:

```
tar → gzip → file.tar.gz
```

tar creates archive stream
gzip compresses stream

---

## Command

```
tar -czf archive.tar.gz folder/
```

Equivalent to:

```
tar -cf - folder/ | gzip > archive.tar.gz
```

Extraction:

```
gzip -d < archive.tar.gz | tar -xf -
```

Or shorthand:

```
tar -xzf archive.tar.gz
```

---

## Why This Design Is Powerful

* tar handles structure
* gzip handles compression
* Each tool does one job

Unix philosophy.

---

# 6. Flag Mechanics (tar)

Flags are positional switches.

Common:

* c = create
* x = extract
* t = list
* f = filename
* z = gzip
* j = bzip2
* J = xz
* v = verbose

---

## f Rule

`f` consumes the next argument.

Correct:

```
tar -czf out.tar.gz folder/
```

Incorrect:

```
tar -cfz out.tar.gz folder/
```

Because `f` sees `z` as filename.

---

## Mental Parsing

```
-czf
 c = create
 z = gzip
 f = file name follows
```

---

# 7. Streaming Behavior

## tar+gzip

```
tar -cz folder/ | ssh server "cat > backup.tar.gz"
```

Works because:

* Sequential stream

---

## zip

Impossible to fully stream because:

* Central directory written at end

---

# 8. Performance Characteristics

## gzip vs zip

gzip:

* One continuous stream
* Better compression for large trees

zip:

* Compresses each file separately
* Lower global compression

---

## CPU

gzip:

* Faster

zip:

* Slightly slower

---

# 9. Metadata Importance (Linux)

Linux filesystems track:

* rwx permissions
* uid/gid
* hardlinks
* symlinks
* sockets
* block/char devices
* xattrs
* ACLs

tar preserves all
zip preserves some

This is decisive.

---

# 10. Package Manager Reality

Linux packages:

* .tar.gz
* .tar.xz
* .tar.zst

Never:

* .zip

Because installers need metadata.

---

# 11. Compatibility Matrix

| Use Case                 | Best Choice |
| ------------------------ | ----------- |
| Linux backups            | tar+gzip    |
| Root filesystem snapshot | tar+gzip    |
| Shipping Linux software  | tar+gzip    |
| Windows sharing          | zip         |
| Email attachment         | zip         |
| Docker layers            | tar         |
| Pipes                    | tar+gzip    |

---

# 12. Modern Alternatives

gzip is old.

Better compressors:

* zstd (fast + good ratio)
* xz (slow + strong)

Example:

```
tar -cJf archive.tar.xz folder/
tar -czstd -f archive.tar.zst folder/
```

Same model:
tar + compressor.

---

# 13. Conceptual Model (Remember This)

ZIP
= single integrated format

TAR+GZIP
= modular pipeline

---

# 14. One-Line Truth

ZIP is a convenience archive.
TAR+GZIP is a filesystem-preserving archive.

---

Concepts 

1 hard links and softlinks
  ln -s and ln hardlink
  
