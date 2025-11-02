# Learning and Git GitHub Cheatsheet

### Tools/Concepts List

---

1. **GNU** – Free OS tools, core for Linux dev.
2. **GCC** – GNU Compiler Collection for C/C++.
3. **GPG** – Encrypts/signs commits and data.
4. **ElectronJS** – Desktop apps using JS, HTML, CSS.
5. **Redis** – In-memory DB, fast caching.
6. **Pydantic** – Data validation in Python via types.
7. **FastAPI** – High-speed Python web API framework.
8. **Resty** – Golang HTTP client lib.
9. **Thonny** – Simple Python IDE.
10. **Teachable Machines** – Google’s no-code ML tool.
11. **Blockly** – Visual block-based coding UI.
12. **Figma** – UI/UX design + real-time collaboration.
13. **WebAssembly** – Binary code for web performance.
14. **UV** – Fast Python package manager (like pip).
15. **Pyodide** – Python runs in browser via WebAssembly.
16. **Worker Runner Test** – Run tests inside browser workers.
17. **Jupyter Lite** – Jupyter in browser, no backend.
18. **LLD** – Low Level Design for scalable systems.
19. **DSA** – Data Structures and Algorithms.
20. **SSH** – Secure remote shell for git access.
21. **Git and GitHub** – Version control + repo hosting.
    - **DCO** – Developer Certificate of Origin (sign-off).
    - **DCO Note:**
        - GitHub username doesn’t matter.
        - Only `user.name` and `user.email` in Git config are verified.
22. making npm packages
23. static reflection
24. what are peer dependency
25. cat command - print a file

---

### Git Commands + Notes

---

**1. View Git History**

- Show last 5 commits (detailed):

    `git log -n 5`

- Show last 5 commits (compact):

    `git log -n 5 --oneline`

- Show full log inline (no pager):

    `git --no-pager log`

- Show last commit only (detailed):

    `git log -1 --pretty=full`

---

**2. Edit Last 5 Commits**

- Interactive rebase (edit/reorder/drop):

    `git rebase -i HEAD~5`

---

**3. Amend Last Commit**

- Change commit message only:

    `git commit --amend -m "new message"`

- Amend and sign-off:

    `git commit --amend -sam "new message"`

---

**4. Auto Stage + Commit + Sign-off**

- One-shot commit:

    `git commit -sam "message"`

---

**5. Alias: Add + Commit + Push**

- Add alias:

    `git config --global alias.cm '!f() { git add . && git commit -sam "$1" && git push; }; f'`

- Then run:

    `git cm "message"`

---

**6. Push Changes**

- Standard push:

    `git push`

- If rejected due to upstream changes:

    `git pull --rebase`

    `git push`

- Force push (use with caution):

    `git push --force`

---

**7. Check Git Identity**

- Global identity:

    `git config --global user.name`

    `git config --global user.email`

- All config locations:

    `git config --list --show-origin`

---

**8. Verify Sign-off in Commit**

- Check for sign-off in last commit:

    `git log -1 --pretty=full`

- Look for:

    `Signed-off-by: Name <email>`

---

**9. Auto Sign Commits Always**

- Enable global sign-off:

    `git config --global format.signoff true`

---

**10. Set Default Branch to Main**

- Set default for future repos:

    `git config --global init.defaultBranch main`

- Rename current branch to main:

    `git branch -m main`

---

**11. Remote Setup and Push**

- Add remote manually:

    `git remote add origin <url>`

- Change remote URL (e.g., if access denied):

    `git remote set-url origin https://github.com/ShardenduMishra22/test-repo.git`

- Push and set upstream:

    `git push --set-upstream origin main`

> 🔹 Note: This is needed only once per branch when setting up manually (not needed if cloned)
>

---

**12. Restore and Stage Files**

- Check file states:

    `git status`

- Discard unstaged changes:

    `git restore <file>`

- Stage changes:

    `git add <file>`

- Unstage staged file:

    `git reset HEAD <file>`

- Restore to last commit version (alt):

    `git checkout HEAD -- <file>`

---

**13. Branching and Merging**

- Create and switch to new branch:

    `git checkout -b feature-1`

- Switch between branches:

    `git switch main`

- Merge branch into current:

    `git merge feature-1`

---

**14. Stashing Changes**

- Save uncommitted changes:

    `git stash`

- Reapply them later:

    `git stash pop`

---

**15. Reset Hard**

- Remove all changes, reset to last commit:

    `git reset --hard`

---

**16. Dual Account SSH Setup Example (`sm22` + `ms22`)**

- You fork from `sm22` → clone to `ms22`:

    `git clone git@github.com:MishraShardendu22/test-repo.git`

    `cd test-repo`

- Add upstream for `sm22`:

    `git remote add upstream git@github.com:ShardenduMishra22/test-repo.git`

- Check remotes:

    `git remote -v`

- Output:

    ```
    origin    → ms22 repo
    upstream  → sm22 original
    
    ```

> 🔹 Cloning with SSH works without error if correct key is loaded for that GitHub account.
>

---

**17. Sync Fork with Upstream**

- Fetch latest from original repo:

    `git fetch upstream`

- Merge into your local branch:

    `git merge upstream/main`

- Push updated branch to your fork:

    `git push origin main`

> 🔹 This lets you update your fork without re-forking.
>

---

**18. Merge Conflict Testing Flow**

- On one branch (`main`):

    `echo 'line A' >> file && git commit -am "main edit"`

- On another branch (`conflict-branch`):

    `echo 'line B' >> file && git commit -am "conflict edit"`

- Merge `conflict-branch` into `main`:

    `git merge conflict-branch`

    (You’ll now resolve conflict manually)

---

**19. Git Repo Version and Tags**

- git tag v1.0.0
- git push origin v1.0.0

---

# 12-Factor Config Management – Technical Summary

## Purpose

Ensure clean separation between **config** (which varies across environments) and **code** (which stays the same). This is critical for safe, consistent, and scalable deployments across environments such as development, staging, and production.

---

## What is Config?

Configuration includes any data that:

- Changes across deploys or environments
- Should not be hardcoded or committed to version control

Examples:

- Database URLs
- External service credentials (e.g., AWS, Stripe, Redis)
- API keys and secrets
- Port numbers
- Hostnames
- Feature flags

---

## What Not to Do

Avoid the following anti-patterns:

- Hardcoding secrets or config values in code files
- Checking `.env` files into version control
- Grouping environment-specific config values in monolithic config files (e.g., using `dev:` / `prod:` sections)
- Using different config formats scattered across the codebase
- Logging or exposing environment variables to clients

---

## Recommended Practice (12-Factor)

- Store each config item as a separate **environment variable**
- Do not group config by environment; treat each variable as independently controlled
- Use `.env` files only for **local development**
- For production or CI/CD:
  - Use system-level environment variable injection
  - Orchestrate secrets through Docker/Kubernetes/CI/CD platforms (GitHub Actions, Render, Heroku, etc.)
- Avoid config files that require parsing or language-specific logic

---

## GoFr-Compatible Directory Structure

```
my-gofr-app/
├── configs/
│   ├── .local.env
│   ├── .dev.env
│   ├── .staging.env
│   └── .prod.env
├── main.go
├── go.mod

```

---

## Configuration Resolution Logic

GoFr uses the `APP_ENV` variable to determine which `.env` file to load:

- If `APP_ENV=dev`, load `configs/.dev.env`
- If `APP_ENV` is unset:
  - Try to load `configs/.local.env`
  - If not found, fallback to `configs/.env`

---

## Sample Environment File (`.dev.env`)

```
APP_NAME=my-dev-service
HTTP_PORT=9001
DB_URL=postgres://user:password@localhost:5432/mydb

```

---

## Example: Environment Loader in Go

```go
func loadEnv() {
 env := os.Getenv("APP_ENV")
 var envFile string

 switch {
 case env != "":
  envFile = "configs/." + env + ".env"
 case fileExists("configs/.local.env"):
  envFile = "configs/.local.env"
 default:
  envFile = "configs/.env"
 }

 err := godotenv.Load(envFile)
 if err != nil {
  log.Fatalf("Failed to load environment file: %s", envFile)
 }
}

func fileExists(path string) bool {
 _, err := os.Stat(path)
 return err == nil
}

```

---

## Environment Variable Safety

Environment variables are secure **only if managed properly**. Avoid leaks by:

- Never printing or logging `os.Getenv` values containing secrets
- Never exposing env vars to client-side code
- Never committing `.env` files to version control

`.env` files are not inherently safer than system environment variables; security depends entirely on your operational discipline.

---

## Why "12-Factor"?

The term comes from the original paper [The Twelve-Factor App](https://12factor.net/), authored by engineers at Heroku. It defines twelve best practices for building robust, maintainable, and cloud-native applications. One of the core principles is managing configuration through environment variables.

---

### The Twelve Factors

- [**Codebase**](https://12factor.net/codebase) – One codebase tracked in version control, many deploys
- [**Dependencies**](https://12factor.net/dependencies) – Explicitly declare and isolate dependencies
- [**Config**](https://12factor.net/config) – Store configuration in the environment
- [**Backing Services**](https://12factor.net/backing-services) – Treat backing services as attached resources
- [**Build, Release, Run**](https://12factor.net/build-release-run) – Strictly separate build, release, and run stages
- [**Processes**](https://12factor.net/processes) – Execute the app as one or more stateless processes
- [**Port Binding**](https://12factor.net/port-binding) – Export services via port binding
- [**Concurrency**](https://12factor.net/concurrency) – Scale out via the process model
- [**Disposability**](https://12factor.net/disposability) – Maximize robustness with fast startup and graceful shutdown
- [**Dev/Prod Parity**](https://12factor.net/dev-prod-parity) – Keep development, staging, and production as similar as possible
- [**Logs**](https://12factor.net/logs) – Treat logs as event streams
- [**Admin Processes**](https://12factor.net/admin-processes) – Run admin/management tasks as one-off processes

---
