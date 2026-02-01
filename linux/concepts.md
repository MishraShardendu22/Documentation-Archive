# Linux Concepts

## Hard Links and Soft Links

### Hard Links

Created with:

```
ln original_file hardlink_name
```

A hard link creates another name for the same inode. Both names point directly to the same data on disk.

Characteristics:

* Same inode number as original
* Cannot cross filesystem boundaries
* Cannot link to directories (in most systems)
* Deleting original doesn't affect the hard link
* Changes to either file affect both

---

### Soft Links (Symbolic Links)

Created with:

```
ln -s original_file softlink_name
```

A soft link (symlink) is a special file that contains a path to another file.

Characteristics:

* Different inode number from original
* Can cross filesystem boundaries
* Can link to directories
* If original is deleted, symlink becomes broken
* Changes to the original reflect in symlink

---
