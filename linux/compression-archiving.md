# ZIP, GZIP, TAR, and TAR+GZIP — Deep Dive

Below are **structured, deep, system-level notes** on ZIP, GZIP, TAR, and TAR+GZIP, including internals, behavior, tradeoffs, and real-world usage.

---

## 1. Fundamental Concepts

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

```md
FILES → ARCHIVE → COMPRESS
```

or

```md
FILES → tar → gzip → archive.tar.gz
```

---

## 2. gzip

## Purpose

Single-file compressor.

## Characteristics

* Input: one file
* Output: one file.gz
* No directory awareness

Example:

```md
gzip data.txt
→ data.txt.gz
```

To restore:

```md
gunzip data.txt.gz
```

or

```md
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

## 3. zip

## Purpose

Combined archiver + compressor.

## Characteristics

* Bundles many files
* Each file compressed separately
* Central directory at end

Example:

```md
zip -r project.zip project/
```

---

## ZIP Internal Structure

```md
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

## 4. tar

## Purpose

Pure archiver.

## Characteristics

* Sequential stream
* No compression

Example:

```md
tar -cf archive.tar folder/
```

---

## TAR Internal Model

```md
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

## 5. tar + gzip

Pipeline:

```md
tar → gzip → file.tar.gz
```

tar creates archive stream
gzip compresses stream

---

## Command

```md
tar -czf archive.tar.gz folder/
```

Equivalent to:

```md
tar -cf - folder/ | gzip > archive.tar.gz
```

Extraction:

```md
gzip -d < archive.tar.gz | tar -xf -
```

Or shorthand:

```md
tar -xzf archive.tar.gz
```

---

## Why This Design Is Powerful

* tar handles structure
* gzip handles compression
* Each tool does one job

Unix philosophy.

---

## 6. Flag Mechanics (tar)

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

```md
tar -czf out.tar.gz folder/
```

Incorrect:

```md
tar -cfz out.tar.gz folder/
```

Because `f` sees `z` as filename.

---

## Mental Parsing

```md
-czf
 c = create
 z = gzip
 f = file name follows
```

---

## 7. Streaming Behavior

## tar+gzip

```md
tar -cz folder/ | ssh server "cat > backup.tar.gz"
```

Works because:

* Sequential stream

---

## zip

Impossible to fully stream because:

* Central directory written at end

---

## 8. Performance Characteristics

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

## 9. Metadata Importance (Linux)

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

## 10. Package Manager Reality

Linux packages:

* .tar.gz
* .tar.xz
* .tar.zst

Never:

* .zip

Because installers need metadata.

---

## 11. Compatibility Matrix

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

## 12. Modern Alternatives

gzip is old.

Better compressors:

* zstd (fast + good ratio)
* xz (slow + strong)

Example:

```md
tar -cJf archive.tar.xz folder/
tar -czstd -f archive.tar.zst folder/
```

Same model:
tar + compressor.

---

## 13. Conceptual Model (Remember This)

ZIP
= single integrated format

TAR+GZIP
= modular pipeline

---

## 14. One-Line Truth

ZIP is a convenience archive.
TAR+GZIP is a filesystem-preserving archive.

---
