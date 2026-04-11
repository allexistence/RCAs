# Git Workflow Tips 

Two common scenarios when contributing to `rancher/rancher-docs`.

---

## Use Case 1: Backporting Changes to Versioned Docs

Backport your PR changes to other supported versions (e.g., v2.10–v2.14), follow these steps.

### Steps

**1. Ensure your local repo is up to date**

```bash
cd <folder>
git fetch origin
git checkout main
git pull origin main
```

**2. Switch to your PR branch**

```bash
git checkout your-branch-name
```

**3. Undo your commit but keep changes staged**

```bash
git reset --soft HEAD~1
```

> This unstages your commit so the backport script can apply the same diff across multiple versioned directories.

**4. Run the backport script from the repo root**

```bash
./backport_docs.sh version-2.10 version-2.11 version-2.12 version-2.13 version-2.14
```

> If the script isn't executable: `chmod +x ./backport_docs.sh`

**5. Review, commit, and push**

```bash
git status
git add .
git commit -s -m "docs: backport <your change description> to v2.10–v2.14"
git push origin your-branch-name --force-with-lease
```

> Always use `--force-with-lease` instead of `--force` — it's safer as it rejects the push if someone else has pushed to the same branch since your last fetch.

---

## Use Case 2: Adding a Missing Sign-Off to Your Last Commit

The `rancher-docs` repo requires a `Signed-off-by` line (DCO) on every commit. If you forgot to include it, you can amend without rewriting your message.

### Steps

**1. Amend the last commit to add the sign-off**

```bash
git commit --amend --signoff --no-edit
```

> `--no-edit` keeps your existing commit message unchanged and just appends:
> `Signed-off-by: Your Name <your@email.com>`

**2. Force push the amended commit**

```bash
git push origin your-branch-name --force-with-lease
```

---

## Tips

- Always use `git commit -s` (shorthand for `--signoff`) when making new commits to avoid forgetting the sign-off.
- Prefer `--force-with-lease` over `--force` when rewriting history on a shared branch.
