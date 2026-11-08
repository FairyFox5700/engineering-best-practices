# CI/CD Practices

## Pipeline design — fail fast

Order pipeline stages from cheapest/fastest to most expensive, so broken changes are rejected in seconds, not minutes:

```
1. Lint / static analysis       (seconds)
2. Type-check / compile         (seconds–low minutes)
3. Unit tests                   (low minutes)
4. Integration tests             (minutes)
5. E2E / smoke tests              (minutes, often in a separate post-merge pipeline)
```

Run stages 1–3 on every push to a PR branch. Reserve slower integration/E2E suites for merge-to-main or a nightly pipeline if they're too slow to run on every push — but never skip them entirely before release.

## PR gating — the minimum bar

Every PR must pass, as required status checks (enforced via branch protection):
- Build succeeds.
- Linter passes (or only produces warnings you've explicitly allowed).
- Unit tests pass with no reduction in coverage below your team's floor.
- No secrets detected (see [security-practices.md](./security-practices.md)).

**Keep the full PR pipeline under ~10 minutes.** Once it crosses that, developers start merging without waiting or bypassing checks — speed is a feature of your CI, not a nice-to-have.

## Flaky tests — treat them as bugs, not noise

- A test that fails intermittently without a code change is a bug in the test or the system under test — not something to "just re-run."
- Quarantine flaky tests immediately: mark them skipped/quarantined in a dedicated suite, file a ticket, and set an SLA (e.g., fixed or deleted within 2 weeks).
- Track flake rate per test in CI dashboards. A growing quarantine list is a leading indicator of eroding test discipline — treat it as tech debt with an owner, not a permanent bucket.
- Never normalize "just click re-run" as standard practice — it hides real race conditions and erodes trust in CI signal.

## Deployment strategies

| Strategy | What it is | When to use |
|---|---|---|
| **Rolling deploy** | Replace instances gradually, old and new versions briefly coexist | Default for most stateless services |
| **Blue-green** | Two full environments; switch traffic atomically from old (blue) to new (green) | When you need instant rollback and can afford double infra briefly |
| **Canary** | Route a small % of traffic to the new version, watch metrics, ramp up | High-traffic/high-risk changes where you want real-traffic validation before full rollout |
| **Feature flags** | Deploy code dark, enable behavior for a subset of users/tenants via config | Decoupling deploy from release; gradual rollout; instant kill-switch without a redeploy |

Feature flags pair naturally with Trunk-Based Development (see [branching-strategies.md](./branching-strategies.md)) — they let you merge continuously while controlling exposure separately from deployment.

## Semantic Versioning

Source: [semver.org](https://semver.org/)

Given a version `MAJOR.MINOR.PATCH`:
- **MAJOR** — incompatible/breaking API changes.
- **MINOR** — backwards-compatible new functionality.
- **PATCH** — backwards-compatible bug fixes.

This maps directly onto Conventional Commit types: `fix:` → PATCH, `feat:` → MINOR, `BREAKING CHANGE`/`!` → MAJOR.

## Automated changelog & release versioning

Because Conventional Commits encode intent, you can automate the entire release process:
- **[semantic-release](https://semantic-release.gitbook.io/)** — analyzes commits since last release, computes the next SemVer version, generates a changelog, publishes (npm, GitHub Releases, etc.) — fully automated, typically run on merge to `main`.
- **[release-please](https://github.com/googleapis/release-please)** (Google) — opens a standing "Release PR" that accumulates changelog entries as commits land; merging that PR cuts the actual release. Preferred when you want a human approval gate before publishing.
- **[conventional-changelog](https://github.com/conventional-changelog/conventional-changelog)** — lower-level tool to just generate `CHANGELOG.md` from commit history; useful if you want changelog generation without full release automation.

**Recommended default:** `release-please` for libraries/services where a human should confirm before publishing; `semantic-release` for high-velocity internal packages where full automation is acceptable.

## CI/CD checklist

- [ ] Fail-fast stage ordering (lint → type-check → unit → integration → e2e)
- [ ] Required status checks enforced via branch protection
- [ ] Full PR pipeline completes in <10 minutes
- [ ] Flaky tests tracked, quarantined, and owned — not ignored
- [ ] Deployment strategy matches risk profile of the service
- [ ] Versioning automated from commit history (SemVer + Conventional Commits)
- [ ] Rollback procedure documented and tested, not just assumed
