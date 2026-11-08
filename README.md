# Engineering Best Practices — Practical Notes

A detailed, actionable reference for day-to-day software engineering practices, synthesized from well-known industry sources (Google's Engineering Practices, Microsoft's Code-With Engineering Playbook, trunkbaseddevelopment.com, Conventional Commits, SemVer, GitHub/GitLab docs). Each topic has its own doc with concrete rules, numbers, templates, and checklists — not just links.

## Contents

| Topic | What's inside |
|---|---|
| [Branching Strategies](./docs/branching-strategies.md) | GitHub Flow, Trunk-Based Development, Git Flow, GitLab Flow — decision guide, concrete workflow steps, feature flags, naming conventions |
| [Commits & Pull Requests](./docs/commits-and-pull-requests.md) | Conventional Commits spec, atomic commits, squash/rebase/merge policy, signing commits, **PR size data from Google (100 vs 1000 lines)**, PR templates, draft PRs |
| [Code Review Guidelines](./docs/code-review-guidelines.md) | Google's standard of review, what to look for, giving feedback, **1-business-day response SLA**, conflict escalation, Microsoft Playbook goals |
| [Repo & Branch Hygiene](./docs/repo-and-branch-hygiene.md) | Auto-delete merged branches, branch protection settings, stale-branch audit scripts, repo archival policy |
| [CI/CD Practices](./docs/ci-cd-practices.md) | Fail-fast pipeline ordering, PR gating, flaky test policy, deployment strategies (canary/blue-green/feature flags), SemVer, automated changelog/release tooling |
| [Testing Strategy](./docs/testing-strategy.md) | Test pyramid ratios, what belongs at each layer, naming conventions, mutation testing, coverage as a floor not a target |
| [Documentation Practices](./docs/documentation-practices.md) | README standards, CONTRIBUTING.md, CODEOWNERS behavior, Architecture Decision Records (ADR) template |
| [Security Practices](./docs/security-practices.md) | Secret scanning + push protection, Dependabot, signed commits, least-privilege tokens |

## Repo contents

```
.github/
  pull_request_template.md   — PR description template (used automatically on new PRs)
  CODEOWNERS                 — example code-ownership mapping
  dependabot.yml              — example Dependabot config
docs/
  branching-strategies.md
  commits-and-pull-requests.md
  code-review-guidelines.md
  repo-and-branch-hygiene.md
  ci-cd-practices.md
  testing-strategy.md
  documentation-practices.md
  security-practices.md
```

## How to use this repo

- Treat it as a living reference, not a one-time read — link specific docs in your own team's CONTRIBUTING.md.
- Copy `.github/pull_request_template.md`, `.github/CODEOWNERS`, and `.github/dependabot.yml` into your own repos as a starting point.
- PRs/suggestions welcome if you find better sources or sharper rules of thumb.

---
*Maintained as a personal reference.*
