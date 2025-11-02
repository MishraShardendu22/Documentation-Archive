# Conventional Commits Guide
This project follows the **Conventional Commits** specification to keep commit history clear and structured.
## Commit Message Format
```
<type>(<optional scope>): <description>
\[optional body]
\[optional footer(s)]
```
---
## Allowed Types
| Type | Badge | Description |
|------|-------|-------------|
| feat | ![feat](https://img.shields.io/badge/feat-new_feature-brightgreen) | New feature |
| fix | ![fix](https://img.shields.io/badge/fix-bug_fix-red) | Bug fix |
| docs | ![docs](https://img.shields.io/badge/docs-documentation-blue) | Documentation changes only |
| style | ![style](https://img.shields.io/badge/style-formatting-lightgrey) | Code style/formatting only, no logic changes |
| refactor | ![refactor](https://img.shields.io/badge/refactor-code_change-yellow) | Code changes without bug fixes or features |
| perf | ![perf](https://img.shields.io/badge/perf-performance-orange) | Performance improvement |
| test | ![test](https://img.shields.io/badge/test-tests-purple) | Adding or updating tests |
| build | ![build](https://img.shields.io/badge/build-dependencies-ff69b4) | Build system or dependency changes |
| ci | ![ci](https://img.shields.io/badge/ci-CI/CD-informational) | CI/CD configuration or script changes |
| chore | ![chore](https://img.shields.io/badge/chore-maintenance-lightblue) | Maintenance tasks (configs, scripts, deps) |
| revert | ![revert](https://img.shields.io/badge/revert-rollback-critical) | Revert a previous commit |
| wip | ![wip](https://img.shields.io/badge/wip-work_in_progress-yellowgreen) | Work in progress (not for production) |
| temp | ![temp](https://img.shields.io/badge/temp-temporary_commit-lightcoral) | Temporary commit, to be squashed later |
| merge | ![merge](https://img.shields.io/badge/merge-branch_merge-silver) | Branch merge commit |
---
## Examples
```
feat(auth): add JWT-based login
fix(ui): correct button alignment on mobile
docs(readme): update installation instructions
style: reformat code using Prettier
refactor(api): extract user service logic
perf(db): optimize user lookup query
test(auth): add unit tests for login endpoint
build(deps): bump axios to v1.3.0
ci(github-actions): add lint check to workflow
chore: clean up unused assets
revert: revert "feat(auth): add JWT-based login"
wip: partial implementation of profile page
temp: temporary commit for debug logging
merge: merge branch 'feature/cart'
```
---
## Breaking Changes
If a commit introduces a breaking change, include the following in the body or footer:
```
BREAKING CHANGE: <explanation>
```
Example:
```
feat(auth): update password hashing algorithm
BREAKING CHANGE: Existing hashed passwords are invalid; users must reset passwords.
