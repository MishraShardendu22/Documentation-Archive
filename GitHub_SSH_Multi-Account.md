# GitHub SSH Multi-Account Permission Fix

## Problem Summary

You may see this error when pushing to a repository:

```
ERROR: Permission to MishraShardendu22/MishraShardendu.git denied to ShardenduMishra22.
fatal: Could not read from remote repository.
```

Even though SSH reports:

```
Hi MishraShardendu22. You've successfully authenticated, but GitHub does not provide shell access.
```

Cause: multiple GitHub accounts with SSH multiplexing active. SSH reuses an existing socket tied to the wrong identity, so GitHub receives the request under a different account.

---

## Root Cause

SSH multiplexing stores active sessions in:

```
~/.ssh/sockets/
```

If a previous session was opened using another account (example: `ShardenduMishra22`), it is reused and overrides your intended key for `MishraShardendu22`.

---

## Solution Steps

### 1. Clear Existing SSH Control Sockets

```bash
rm -rf ~/.ssh/sockets/*
```

### 2. Restart SSH Agent With Correct Key

```bash
ssh-add -D
ssh-add ~/.ssh/id_ed25519_project
```

Modify the key filename if your personal key differs.

### 3. Test SSH Connection

```bash
ssh -T git@github.com-project
```

Expected:

```
Hi MishraShardendu22. You've successfully authenticated, but GitHub does not provide shell access.
```

If not, the wrong identity is still loaded.

### 4. Push to the Repository

```bash
git push origin main
```

---

## SSH Config Reference

Save in `~/.ssh/config`:

```
# Work Account
Host github.com-work
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519_work
  IdentitiesOnly yes
  ControlMaster auto
  ControlPath ~/.ssh/sockets/%r@%h:%p
  ControlPersist 30m

# Project Account (Primary: MishraShardendu22)
Host github.com-project
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519_project
  IdentitiesOnly yes
  ControlMaster auto
  ControlPath ~/.ssh/sockets/%r@%h:%p
  ControlPersist 30m

# Learning Account
Host github.com-learning
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519_learning
  IdentitiesOnly yes
  ControlMaster auto
  ControlPath ~/.ssh/sockets/%r@%h:%p
  ControlPersist 30m
```

No typos. No duplicate Host entries. No invalid characters.

---

## Verify Remote URL

Check:

```bash
git remote -v
```

Expected:

```
origin  git@github.com-project:MishraShardendu22/MishraShardendu.git (fetch)
origin  git@github.com-project:MishraShardendu22/MishraShardendu.git (push)
```

Fix if incorrect:

```bash
git remote set-url origin git@github.com-project:MishraShardendu22/MishraShardendu.git
```

---

## Optional Fix Script

`scripts/fix-github-ssh.sh`:

```bash
#!/bin/bash

rm -rf ~/.ssh/sockets/*

ssh-add -D
ssh-add ~/.ssh/id_ed25519_project

ssh -T git@github.com-project
```

Make executable:

```bash
chmod +x scripts/fix-github-ssh.sh
```

---

## Summary Table

| Step | Action                 | Command                                           |
| ---- | ---------------------- | ------------------------------------------------- |
| 1    | Clear old SSH sessions | `rm -rf ~/.ssh/sockets/*`                         |
| 2    | Reload key             | `ssh-add -D && ssh-add ~/.ssh/id_ed25519_project` |
| 3    | Test connection        | `ssh -T git@github.com-project`                   |
| 4    | Push                   | `git push origin main`                            |

---
