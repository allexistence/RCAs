# RCA: DCO Check Failure on Helm PR #31838

## Summary
The pull request failed the **DCO (Developer Certificate of Origin)** check because the commit was missing the required `Signed-off-by:` line. This is a mandatory requirement for Helm and other CNCF projects to ensure contributors certify authorship and agree to the project’s license.

---

## What happened
- The PR was created successfully.
- All build, lint, and unit tests passed.
- However, the **DCO required** check failed.
- GitHub reported that one or more commits were missing the required sign-off.

Example error:

```
DCO required — There is one commit incorrectly signed off.
```

---

## Root Cause
The commit was created without using the sign-off flag:

```bash
git commit -m "fix: clarify coalesce warnings about maps vs non-maps"
```

This caused the commit message to not include the required line:

```
Signed-off-by: Rishabh Pandey <your-email>
```

Helm requires this line to comply with CNCF contribution policies.

---

## Why DCO is required

DCO confirms that the contributor:

- Authored the code themselves, OR  
- Has the legal right to contribute the code, AND  
- Agrees to the project’s open-source license (Apache 2.0)  

This is standard across CNCF projects like Helm, Kubernetes, containerd, Prometheus, and Linux kernel.

---

## Resolution

The issue was fixed by amending the commit and adding the sign-off:

```bash
git commit --amend -s
git push --force-with-lease origin fix-coalesce-table-warning
```

This automatically added the required sign-off line:

```
Signed-off-by: Rishabh Pandey <your-email>
```

---

## Verification

Verify the commit contains the sign-off:

```bash
git log -1
```

Expected output includes:

```
Signed-off-by: Rishabh Pandey <your-email>
```

After this, the DCO check passed successfully.

---

## Prevention (Best Practice)

Always commit with sign-off:

```bash
git commit -s -m "your commit message"
```

Optional: create a Git alias:

```bash
git config --global alias.cis "commit -s"
```

Then use:

```bash
git cis -m "your commit message"
```

---

## Impact

- No functional or code impact.
- Only PR merge was blocked temporarily.
- Resolved quickly by amending the commit.

---

## Lessons Learned

- Always include sign-off when contributing to CNCF projects.
- DCO compliance is mandatory for Helm contributions.
- Using `git commit -s` prevents CI failures.

---

## Status

✅ Resolved  
DCO check passed and PR is ready for maintainer review and merge.
