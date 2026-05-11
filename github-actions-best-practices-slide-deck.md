---
marp: true
theme: default
paginate: true
---

# GitHub Actions Best Practices
## 10 practical habits for reliable CI/CD

Presenter note: ~1 minute per slide.

---

# 1) Keep workflows focused and modular
- Separate CI, CD, and maintenance workflows.
- Reuse logic with `workflow_call` reusable workflows.
- Keep each workflow easy to read and troubleshoot.

---

# 2) Trigger only when needed
- Use `on.push.paths`, `paths-ignore`, and branch filters.
- Prefer targeted workflows over "run on everything".
- Reduces queue time and compute cost.

---

# 3) Pin actions to immutable SHAs
- Use commit SHAs for third-party actions.
- Update pins intentionally on a schedule.
- Prevents supply-chain surprises.

---

# 4) Lock down permissions by default
- Start with least privilege: `permissions: {}`.
- Elevate only per job when writes are required.
- Avoid broad default `GITHUB_TOKEN` access.

---

# 5) Protect secrets and avoid long-lived credentials
- Store secrets in GitHub Secrets/Environments.
- Prefer OpenID Connect (OIDC) + cloud short-lived tokens.
- Never echo secrets to logs.

---

# 6) Use environment protections for deployments
- Require reviewers for production environments.
- Use wait timers and protected branches.
- Add clear promotion gates (dev -> stage -> prod).

---

# 7) Speed up workflows with caching and matrices
- Cache dependencies (`actions/cache`, built-in setup cache).
- Use matrix builds for OS/runtime coverage.
- Combine with `fail-fast` settings intentionally.

---

# 8) Prevent duplicate work with concurrency controls
- Use `concurrency` groups per branch/PR.
- Enable `cancel-in-progress: true` for fast iteration.
- Keep only the latest relevant run active.

---

# 9) Make observability first-class
- Name jobs/steps clearly.
- Upload artifacts and test reports for debugging.
- Use summaries (`GITHUB_STEP_SUMMARY`) for quick outcomes.

---

# 10) Standardize quality and security checks
- Require status checks before merge.
- Add CodeQL/Dependency review/secret scanning in CI.
- Treat workflows as code: review, test, and version them.

---

# Closing
## Start small: pick 2 best practices this week

1. Tighten token permissions
2. Add SHA pinning policy
3. Enable concurrency cancellation
