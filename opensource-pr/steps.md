# Git Open Source Workflow Guide
> **Goal:** Raise PRs, manage forks, and update branches — all without merge conflicts.

---

## Terms
| Term | Meaning |
|------|---------|
| `upstream` | Original repo e.g. `kubernetes/website` |
| `origin` | Your fork e.g. `your-username/website` |
| `main` | Default branch (sometimes `master`) |
| `feature-branch` | Your working PR branch |

---

## ⚙️ One-Time Setup (Do this once per repo)

```bash
# 1. Clone your fork
git clone https://github.com/<your-username>/<repo>.git
cd <repo>

# 2. Add upstream remote
git remote add upstream https://github.com/<upstream-org>/<repo>.git

# 3. Verify both remotes exist
git remote -v
# origin    https://github.com/<your-username>/<repo>.git (fetch)
# origin    https://github.com/<your-username>/<repo>.git (push)
# upstream  https://github.com/<upstream-org>/<repo>.git (fetch)
# upstream  https://github.com/<upstream-org>/<repo>.git (push)
```

---

## Use Case 1 — First Time Raising a PR to Upstream Repo

> You have just forked the repo and want to raise your first PR.

```bash
# Step 1: Sync local main with upstream BEFORE branching
# Use reset --hard instead of pull to avoid extra merge commits on main
git checkout main
git fetch upstream
git reset --hard upstream/main
git push origin main --force-with-lease

# Step 2: Create a new feature branch from the updated main
# Never commit directly to main — always work on a feature branch
git checkout -b docs/add-recommended-labels-example

# Step 3: Make your changes in your editor, then verify
git status
git diff

# Step 4: Stage and commit (use -s for DCO sign-off, required in CNCF/K8s repos)
git add <files-you-changed>
git commit -s -m "docs: add recommended labels example"

# Step 5: Push branch to your fork and set upstream tracking
git push -u origin docs/add-recommended-labels-example

# Step 6: Open PR on GitHub
# → Go to your fork on GitHub
# → Click "Compare & pull request"
# → Base repo  : <upstream-org>/<repo>  |  Base branch : main
# → Compare    : <your-username>:<feature-branch>
# → Fill in PR title and description
# → Submit the PR
```

> ✅ **Conflict prevention:** Always pull from `upstream/main` before creating any branch.

---

## Use Case 2 — Repo Already Forked: Raise a New PR

> Your fork exists. You want to start a fresh PR for a different change.

```bash
# Step 1: Always start from an up-to-date main (critical!)
# Use reset --hard instead of pull to avoid extra merge commits on main
git checkout main
git fetch upstream
git reset --hard upstream/main
git push origin main --force-with-lease

# Step 2: Create a fresh branch for the new PR
# Always branch off the freshly synced main
git checkout -b fix/typo-labels-page

# Step 3: Make changes, verify, commit
git status
git diff

git add <files>
git commit -s -m "fix: correct typo on labels page"

# Step 4: Push the new branch to your fork
git push -u origin fix/typo-labels-page

# Step 5: Open PR on GitHub (same as Use Case 1, Step 6)
# → Go to your fork on GitHub
# → Click "Compare & pull request"
# → Base repo  : <upstream-org>/<repo>  |  Base branch : main
# → Compare    : <your-username>:fix/typo-labels-page
# → Fill in PR title and description
# → Submit the PR
```

> ✅ **Conflict prevention:** Never branch off an old local `main`. Always rebase against `upstream/main` first.

---

## Use Case 3 — Add New Commits to an Existing Branch / Open PR

> You already have a PR open and need to push more changes to it safely.

```bash
# Step 1: Switch to your PR branch
git checkout docs/add-recommended-labels-example

# Step 2: Fetch upstream and rebase to stay current (avoids conflicts)
git fetch upstream
git rebase upstream/main

# --- If rebase hits conflicts ---
# Git will pause and show conflicting files. Resolve them manually, then:
git status                        # shows which files need resolving
git add <conflicted-file>         # mark each file resolved one by one
git rebase --continue             # move to next commit in rebase
# Repeat git add + git rebase --continue until rebase completes
# To cancel rebase entirely and go back to original state:
# git rebase --abort

# Step 3: Make your new edits, then verify
git status
git diff

# Step 4: Stage and commit the new changes
git add <files>
git commit -s -m "docs: address review feedback on labels example"

# Step 5: Check your commit count on this branch
# You should ideally have only 1 clean commit before pushing
git log --oneline upstream/main..HEAD

# --- If you have multiple commits, squash them into one ---
# --soft resets commits but keeps all changes staged (your work is NOT lost)
git reset --soft upstream/main
git add .
git commit -s -m "docs: add recommended labels example"

# Step 6: Push updates back to the same PR branch
# If you did NOT rebase or squash:
git push origin docs/add-recommended-labels-example

# If you DID rebase or squash (history was rewritten — force push required):
git push --force-with-lease origin docs/add-recommended-labels-example
# ✅ --force-with-lease is safer than --force: it fails if someone else pushed in the meantime
```

> ✅ **Conflict prevention:** Rebase against `upstream/main` before every push. The PR updates automatically.

---

## 🔁 No-Conflict Checklist (Run Before Every Push)

```bash
# 1. Confirm you are on the right branch
git branch

# 2. Fetch latest upstream and rebase
git fetch upstream
git rebase upstream/main

# 3. Confirm working tree is clean
git status
```

---

## 🧹 After PR is Merged — Cleanup

```bash
# 1. Sync main with upstream
git checkout main
git fetch upstream
git pull upstream main --rebase
git push origin main

# 2. Delete the feature branch locally
git branch -d docs/add-recommended-labels-example

# 3. Delete the branch from your fork (optional)
git push origin --delete docs/add-recommended-labels-example
```

---

## 📋 Quick Reference

| Task | Command |
|------|---------|
| See what changed | `git diff` / `git diff --staged` |
| Recent commits | `git log --oneline -10` |
| Unstage a file (keep edits) | `git restore --staged <file>` |
| Undo local edits (⚠️ destructive) | `git restore <file>` |
| Safe force push | `git push --force-with-lease origin <branch>` |

---

## 💡 Branch Naming Examples

```
docs/fix-labels-example
docs/add-argocd-install-note
fix/typo-labels-page
feat/add-keda-scaler-example
```
