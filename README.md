# Engineering Best Practices — Practical Notes

A living, no-nonsense reference for day-to-day software engineering hygiene: branching, PRs, code review, and repo maintenance. Written to be actionable, not theoretical.

## Table of Contents
- [Branching](#branching)
- [Commits & Pull Requests](#commits--pull-requests)
- [Code Review](#code-review)
- [Keeping Repos Clean](#keeping-repos-clean)
- [CI/CD Basics](#cicd-basics)
- [Further Reading](#further-reading)

---

## Branching

**Pick one model and stick to it:**
- **GitHub Flow** (recommended for most teams): `main` is always deployable. Create a short-lived feature branch per change, open a PR, merge, delete the branch. No long-lived `develop`/`release` branches.
- **Trunk-Based Development**: even shorter-lived branches (hours, not days), frequent merges to `main`, feature flags instead of long branches for incomplete work.
- **Git Flow**: heavier model with `develop`, `release/*`, `hotfix/*`. Only use this if you ship versioned releases (e.g., libraries, mobile apps with app-store review cycles).

**Branch naming convention** (pick something consistent):
```
feature/short-description
fix/bug-ticket-id
chore/dependency-update
spike/exploration-name
```

**Rules of thumb:**
- Branch from latest `main`, rebase often to avoid drift.
- Keep branches short-lived (days, not weeks). Long branches = merge pain.
- One logical change per branch/PR. Don't bundle unrelated fixes.

## Commits & Pull Requests

- Write commit messages in imperative mood: `Fix null check in parser`, not `Fixed` or `Fixing`.
- Squash noisy WIP commits before merge; keep history readable (`git rebase -i`).
- PR description should answer: **what** changed, **why**, and **how to verify**.
- Keep PRs small — aim for <400 lines changed. Big PRs get rubber-stamped, not reviewed.
- Link the PR to an issue/ticket when one exists.

## Code Review

- Review for correctness, security, and maintainability — not style (let linters/formatters handle style).
- Leave actionable comments, not just "this is wrong." Suggest an alternative.
- Approve with comments for nits; block only for real issues.
- Authors: respond to every comment (even if just "done" or "disagree because X").
- Aim for review turnaround < 24h to avoid blocking teammates.

## Keeping Repos Clean

**Delete merged branches automatically:**
- GitHub: Settings → General → "Automatically delete head branches" (enable this on every repo).
- GitLab: Settings → Merge requests → "Delete source branch when merge request is accepted."

**Periodically prune stale branches:**
```bash
# List branches merged into main, safe to delete
git branch --merged main | grep -v "main"

# Delete local branches already merged
git branch --merged main | grep -v "main" | xargs -r git branch -d

# Prune remote-tracking branches that no longer exist on origin
git fetch --prune
```

**Stale branch audit (branches untouched >90 days):**
```bash
for branch in $(git for-each-ref --format='%(refname:short)' refs/remotes/origin); do
  echo "$(git log -1 --format="%ci" "$branch") $branch"
done | sort
```

- Set up a recurring reminder/automation (monthly) to review and close stale PRs and delete abandoned branches.
- Use branch protection rules on `main`: require PR review, require status checks to pass, disallow force-push.

## CI/CD Basics

- Every PR should trigger: build, lint, unit tests — minimum bar before merge is allowed.
- Keep pipelines fast (<10 min) or developers will bypass them.
- Fail fast: run cheap checks (lint, type-check) before expensive ones (integration tests).
- Treat flaky tests as bugs — quarantine or fix them immediately, don't let the team normalize re-running CI.

## Further Reading

- [GitHub Flow](https://docs.github.com/en/get-started/using-github/github-flow)
- [Trunk Based Development](https://trunkbaseddevelopment.com/)
- [Atlassian: Comparing Git workflows](https://www.atlassian.com/git/tutorials/comparing-workflows)
- [Google Engineering Practices (code review)](https://google.github.io/eng-practices/)
- [Microsoft Code-With Engineering Playbook](https://microsoft.github.io/code-with-engineering-playbook/)
- [thoughtbot Playbook](https://thoughtbot.com/playbook)

---
*Maintained as a personal reference. PRs/suggestions welcome.*
