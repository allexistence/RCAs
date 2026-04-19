# Git Rebase Guide

This document explains how to use `git rebase` safely to keep your pull request branch clean and up to date.

---

## What rebase does

`git rebase` moves your branch commits to a new base commit.

In PR workflows, this is usually used to replay your branch commits on top of the latest `upstream/main` (or `upstream/master`).

Benefits:

- keeps commit history linear
- avoids unnecessary merge commits
- makes review and troubleshooting easier

---

## Common rebase flow for an open PR

```bash
git checkout <your-feature-branch>
git fetch upstream
git rebase upstream/main
```

If your upstream default branch is `master`, use:

```bash
git rebase upstream/master
```

---

## Conflict resolution during rebase

If Git reports conflicts:

1. Open and resolve each conflicted file.
2. Mark files as resolved:

   ```bash
   git add <resolved-file>
   ```

3. Continue rebase:

   ```bash
   git rebase --continue
   ```

4. Repeat until rebase finishes.

If you want to cancel and return to the pre-rebase state:

```bash
git rebase --abort
```

---

## Push after rebase

Rebase rewrites commit history, so a force push is required:

```bash
git push --force-with-lease origin <your-feature-branch>
```

Use `--force-with-lease` instead of `--force` for safer updates.

---

## Interactive rebase (squash/edit/reword)

Use interactive rebase to clean up commit history before merge:

```bash
git rebase -i HEAD~<n>
```

In the interactive editor:

- `pick` keeps a commit as is
- `squash` combines a commit with the one above
- `reword` keeps commit changes but lets you edit message
- `drop` removes a commit

After saving, Git applies the updated plan.

---

## Best practices

- Rebase before pushing updates to an open PR.
- Keep PRs focused and commit history readable.
- Avoid rebasing shared branches used by multiple collaborators unless coordinated.
- Always run `git status` after rebase to confirm clean state.
