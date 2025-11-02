# Multi-Account GitHub Setup with SSH, Verified Commits, and DCO

## 1. Overview

When managing multiple GitHub accounts (personal projects, learning, and work) on one machine, there are **two separate identity layers** you must manage:

1. **SSH identity** → Which GitHub account you authenticate as (**access control**).
2. **Git config identity** → The name/email that appears inside your commits (**authorship**).

These are independent. If misconfigured, you can push with the wrong account or create unverified commits.

This guide covers:

* SSH key setup for multiple accounts.
* Configuring `~/.ssh/config` for clean account separation.
* Setting Git config per account with shell helpers.
* Enabling **SSH commit signing** for verified commits.
* Adding **DCO (Developer Certificate of Origin)** automatically.
* Git aliases to streamline workflows.

---

## 2. SSH Keys per Account

Generate a unique SSH key for each GitHub account:

```bash
ssh-keygen -t ed25519 -C "email-for-project" -f ~/.ssh/id_ed25519_project
ssh-keygen -t ed25519 -C "email-for-learning" -f ~/.ssh/id_ed25519_learning
ssh-keygen -t ed25519 -C "email-for-work" -f ~/.ssh/id_ed25519_work
```

* Do **not overwrite** existing keys.
* Upload the **public key** (`.pub`) to each GitHub account under **Settings → SSH and GPG Keys**.

---

## 3. SSH Config

Define aliases in `~/.ssh/config`:

```ssh
# Work account
Host github.com-work
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519_work
  IdentitiesOnly yes

# Project account
Host github.com-project
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519_project
  IdentitiesOnly yes

# Learning account
Host github.com-learning
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519_learning
  IdentitiesOnly yes
```

### How it works

* `git@github.com-project:USERNAME/REPO.git` → uses the **project key only**.
* If you just use `git@github.com:USERNAME/REPO.git` (no alias):

  * SSH sees `github.com`.
  * Ignores aliases.
  * Tries all keys until one works → may authenticate with the wrong account.

### Test which key is used:

```bash
ssh -T git@github.com-project
ssh -T git@github.com-learning
ssh -T git@github.com-work
```

Expected:
`Hi <username>! You've successfully authenticated, but GitHub does not provide shell access.`

---

## 4. Git Config Identity

Each commit records the author:

```bash
git config user.name "Your Name"
git config user.email "your@email.com"
```

* `user.name` + `user.email` are stored inside commits.
* **Not linked** to SSH keys.
* If `user.email` isn’t verified in the GitHub account you’re pushing with, the commit shows as **unverified**.

### Example mismatch

* SSH alias = `github.com-project` → Authenticates as `MishraShardendu22`.
* Git config = `user.email = shardendumishra01@gmail.com` (learning account).
* Commit author = learning account, but push done via project account.

Result: GitHub accepts push but commit may show as **unverified or misattributed**.

---

## 5. Account-Specific Helpers in `.bashrc`

Add helper functions to `~/.bashrc`:

```bash
# Project account
useproject() {
  git config user.name 'MishraShardendu22'
  git config user.email 'shardendumishrabrave@gmail.com'
  git config commit.gpgsign false
  git config format.signoff true
}

# Learning account
uselearn() {
  git config user.name 'ShardenduMishra22'
  git config user.email 'shardendumishra01@gmail.com'
  git config commit.gpgsign false
  git config format.signoff true
}

# Work account
useccwork() {
  git config user.name 'Work 1'
  git config user.email 'work1@gmail.com'
  git config commit.gpgsign false
  git config format.signoff true
}
```

Reload:

```bash
source ~/.bashrc
```

Usage per repo:

```bash
cd my-repo
useproject
```

---

## 6. Verified Commits with SSH Signing

GitHub allows commits to be signed with **SSH keys**:

```bash
git config commit.gpgsign true
git config gpg.format ssh
git config user.signingkey ~/.ssh/id_ed25519_project.pub
```

Repeat for each repo with the correct key.
Now GitHub shows **“Verified”** next to your commits.

---

## 7. Automatic DCO Sign-off

The **DCO (Developer Certificate of Origin)** adds a footer:

```
Signed-off-by: MishraShardendu22 <shardendumishrabrave@gmail.com>
```

Enable automatically:

```bash
git config format.signoff true
```

Or add `-s` when committing:

```bash
git commit -s -m "message"
```

---

## 8. Git Aliases for Speed

### Auto commit with DCO

```bash
git config --global alias.c "commit -s"
```

Usage:

```bash
git c -m "fix bug"
```

### One-shot add, commit, and push

```bash
git config --global alias.cm '!f() { git add . && git commit -s -m "$1" && git push; }; f'
```

Usage:

```bash
git cm "update readme"
```

---

## 9. Useful Checks

* Remote URL:

  ```bash
  git remote -v
  ```
* Which SSH key will be used:

  ```bash
  ssh -T git@github.com-project
  ```
* Current Git identity:

  ```bash
  git config user.name
  git config user.email
  ```

---

## 10. Key Takeaways

* **SSH alias controls access** (which GitHub account you push as).
* **Git config controls authorship** (what shows up in commits).
* They are independent → always keep them in sync.
* Always clone with the correct alias (`github.com-project`, `github.com-work`, etc.).
* Use helper functions (`useproject`, `uselearn`, `useccwork`) to set the correct identity per repo.
* Enable **SSH signing** for verified commits.
* Enable **DCO sign-off** for compliance.
* Use Git aliases to speed up workflow.

---

## 11. Example Workflow

```bash
# Clone with the right account
git clone git@github.com-project:MishraShardendu22/my-repo.git

# Switch identity
useproject

# Work normally
git cm "add login feature"
```

Commit will show on GitHub as:

* **Verified** (SSH signed).
* **Signed-off-by** footer (DCO).
* Correct account attribution.
