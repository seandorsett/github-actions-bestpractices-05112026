---
marp: true
theme: default
paginate: true
---

# GitHub Actions Best Practices
## 15 practical habits for reliable CI/CD

Presenter note: ~1 minute per slide.

---

# 1) Keep workflows focused and modular
- Separate CI, CD, and maintenance workflows.
- Reuse logic with `workflow_call` reusable workflows.
- Keep each workflow easy to read and troubleshoot.
- Example: `ci.yml` runs tests, while `deploy.yml` is only triggered after CI succeeds.

<!--
Presenter notes:
Explain that focused workflows reduce blast radius and debugging time. Mention that reusable workflows prevent copy/paste drift.
-->

---

# 2) Trigger only when needed
- Use `on.push.paths`, `paths-ignore`, and branch filters.
- Prefer targeted workflows over "run on everything".
- Reduces queue time and compute cost.
- Example: docs-only changes skip backend integration tests using `paths-ignore: ['docs/**']`.

<!--
Presenter notes:
Call out the ROI: fewer unnecessary runs means faster feedback and lower CI spend.
-->

---

# 3) Pin actions to immutable SHAs
- Use commit SHAs for third-party actions.
- Update pins intentionally on a schedule.
- Prevents supply-chain surprises.
- Example: use `actions/checkout@8ade135...` instead of a floating `@v4` tag.

<!--
Presenter notes:
Stress security posture: tags can move, SHAs cannot. Mention using Dependabot to help with updates.
-->

---

# 4) Lock down permissions by default
- Start with least privilege: `permissions: {}`.
- Elevate only per job when writes are required.
- Avoid broad default `GITHUB_TOKEN` access.
- Example: grant only `contents: read` for test jobs, and `pull-requests: write` only for comment bots.

<!--
Presenter notes:
Reinforce principle of least privilege and how per-job permissions reduce damage from compromised steps.
-->

---

# 5) Protect secrets and avoid long-lived credentials
- Store secrets in GitHub Secrets/Environments.
- Prefer OpenID Connect (OIDC) + cloud short-lived tokens.
- Never echo secrets to logs.
- Example: AWS deploy job assumes an IAM role via OIDC instead of storing static AWS keys.

<!--
Presenter notes:
Highlight secret rotation burden and how OIDC removes long-lived credentials from repo settings.
-->

---

# 6) Use environment protections for deployments
- Require reviewers for production environments.
- Use wait timers and protected branches.
- Add clear promotion gates (dev -> stage -> prod).
- Example: production environment requires one approver before the deploy job can execute.

<!--
Presenter notes:
Frame this as a safety brake that catches mistakes right before release.
-->

---

# 7) Speed up workflows with caching and matrices
- Cache dependencies (`actions/cache`, built-in setup cache).
- Use matrix builds for OS/runtime coverage.
- Combine with `fail-fast` settings intentionally.
- Example: run Node tests on `{18,20}` with npm cache enabled to reduce install time.

<!--
Presenter notes:
Discuss balancing speed and confidence: cache for performance, matrix for compatibility coverage.
-->

---

# 8) Prevent duplicate work with concurrency controls
- Use `concurrency` groups per branch/PR.
- Enable `cancel-in-progress: true` for fast iteration.
- Keep only the latest relevant run active.
- Example: new commits to a PR automatically cancel older in-progress CI runs.

<!--
Presenter notes:
Mention developer experience improvement: less waiting for outdated runs and cleaner signal in checks.
-->

---

# 9) Make observability first-class
- Name jobs/steps clearly.
- Upload artifacts and test reports for debugging.
- Use summaries (`GITHUB_STEP_SUMMARY`) for quick outcomes.
- Example: upload failed Playwright screenshots and a markdown summary with pass/fail counts.

<!--
Presenter notes:
If a run fails at 2 a.m., logs and artifacts should make root cause obvious without rerunning.
-->

---

# 10) Standardize quality and security checks
- Require status checks before merge.
- Add CodeQL/Dependency review/secret scanning in CI.
- Treat workflows as code: review, test, and version them.
- Example: branch protection enforces CodeQL + unit tests before merge to `main`.

<!--
Presenter notes:
Position this as creating a consistent quality bar rather than relying on reviewer memory.
-->

---

# 11) Enforce timeouts and retry strategy
- Set `timeout-minutes` for jobs and risky steps.
- Add retries only where failures are transient.
- Fail fast on deterministic errors.
- Example: integration test job has `timeout-minutes: 20`; flaky network download step retries twice.

<!--
Presenter notes:
Explain that stuck jobs consume capacity and hide real failures. Timeouts keep pipelines healthy.
-->

---

# 12) Use artifacts and outputs to share data safely
- Prefer job `outputs` and uploaded artifacts over ad-hoc temp files.
- Keep interfaces explicit between jobs.
- Retain artifacts for an intentional period.
- Example: build job uploads `app.tar.gz`; deploy job downloads that exact artifact.

<!--
Presenter notes:
Emphasize reproducibility: deploy exactly what was tested, not a rebuilt variant.
-->

---

# 13) Make self-hosted runners secure and ephemeral
- Isolate runners by trust level and repository scope.
- Patch runner images and lock down network access.
- Prefer ephemeral runners for untrusted code.
- Example: pull requests from forks run on GitHub-hosted runners; internal release jobs use isolated self-hosted runners.

<!--
Presenter notes:
Call out that runner compromise risk is real; segmentation and ephemeral hosts reduce persistence.
-->

---

# 14) Use reusable workflow templates and versioning
- Publish common workflows in a central repo.
- Reference reusable workflows by SHA/tag policy.
- Version and roll out updates intentionally.
- Example: all services call `org/platform/.github/workflows/node-ci.yml@v3` for consistent CI behavior.

<!--
Presenter notes:
Focus on scaling practices across teams while keeping change management controlled.
-->

---

# 15) Measure and continuously improve pipeline health
- Track lead time, queue time, and flaky test rate.
- Review top failure causes and automate fixes.
- Set quarterly workflow improvement goals.
- Example: team dashboard shows median CI duration dropped 30% after cache and test-splitting improvements.

<!--
Presenter notes:
Close with continuous improvement mindset: workflows are products that need metrics and iteration.
-->

---

# Closing
## Start small: pick 2 best practices this week

1. Tighten token permissions
2. Add SHA pinning policy
3. Enable concurrency cancellation
