# Commits & Pull Requests

## Commit message convention: Conventional Commits

Source: [conventionalcommits.org v1.0.0](https://www.conventionalcommits.org/en/v1.0.0/)

**Format:**
```
<type>[optional scope]: <description>

[optional body]

[optional footer(s)]
```

**Rules:**
- `fix:` → a bug patch (maps to SemVer `PATCH`).
- `feat:` → a new feature (maps to SemVer `MINOR`).
- Breaking change → either a `!` right after type/scope (`feat!:`) or a footer `BREAKING CHANGE: <description>` (maps to SemVer `MAJOR`).
- Other common types (from the Angular convention, widely adopted): `build:`, `chore:`, `ci:`, `docs:`, `style:`, `refactor:`, `perf:`, `test:`.
- Optional `(scope)`: `fix(parser): handle empty arrays`.
- Footers follow git trailer format: `Reviewed-by: Jane`, `Refs: #123`.

**Examples:**
```
feat(auth): add refresh token rotation

fix: prevent race condition on duplicate submit

Introduce a request id and compare against the latest request.
Discard stale responses.

Refs: #482
```
```
feat(api)!: remove deprecated /v1/users endpoint

BREAKING CHANGE: clients must migrate to /v2/users before upgrading.
```

**Why bother:**
- Enables automated CHANGELOG generation (`conventional-changelog`, `release-please`, `semantic-release`).
- Enables automatic SemVer bumps based on commit types landed since last release.
- Makes `git log` actually useful for humans scanning history.

**Style baseline (even without Conventional Commits):**
- Imperative mood: `Fix null check`, not `Fixed` / `Fixing`.
- Subject line ≤ 50 chars, body wrapped at ~72 chars, blank line between them.
- Explain **why**, not just what — the diff already shows what changed.

## Atomic commits

Each commit should represent one complete, logical, buildable change. If you'd need to revert two commits together to undo one idea, they should have been one commit. This makes `git bisect`, `git revert`, and code archaeology actually work.

## Squash vs. merge vs. rebase — pick a per-repo policy

| Strategy | What happens | When to use |
|---|---|---|
| **Squash and merge** | All PR commits become one commit on `main` | Default for most teams — keeps `main` history clean and linear, PR = one commit |
| **Rebase and merge** | PR commits are replayed individually onto `main`, no merge commit | When you want granular history preserved and commits are already clean/atomic |
| **Merge commit** | Creates an explicit merge commit preserving full branch history | Rarely needed outside Git Flow release/hotfix merges |

Most GitHub Flow / Trunk-Based teams: **squash-merge PRs**, enforce it as the only allowed merge method in repo settings (Settings → General → "Allow squash merging" only).

## Signing commits

Use GPG/SSH commit signing so merges show as "Verified" on GitHub. Required by many orgs for compliance (e.g., SOC2, supply-chain security).
```bash
git config --global commit.gpgsign true
git config --global user.signingkey <KEY_ID>
```
Can be enforced org-wide via branch protection ("Require signed commits").

---

## Pull Request size

Source: [Google eng-practices — Small CLs](https://google.github.io/eng-practices/review/developer/small-cls.html)

**Concrete numbers from Google's internal guidance:**
- **~100 lines is usually a reasonable size** for a change.
- **~1000 lines is usually too large.**
- File count matters too: "A 200-line change in one file might be okay, but spread across 50 files it would usually be too large."
- Reviewers have explicit license to **reject a CL outright for being too large**, regardless of code quality.
- Deleting an entire file counts as ~1 line of review effort — large deletions are an exception to the size rule.
- Large, trusted auto-refactors (e.g. codemods) are another accepted exception.

**Why small PRs win** (same source):
- Reviewed faster — reviewers can find 5 minutes several times rather than blocking out 30+ minutes.
- Reviewed more thoroughly — less chance important comments get lost in a wall of feedback.
- Fewer bugs — smaller diffs are easier to reason about.
- Less wasted work if direction is rejected.
- Easier to merge (fewer conflicts, less staleness).
- Easier to roll back cleanly.

**Practical target:** keep PRs under ~400 lines changed where possible. If a change doesn't fit, split it — e.g., land the plumbing/interface first, then the implementation, then the call sites.

## PR description template

Put this in `.github/pull_request_template.md` (already included in this repo):
- **What** changed.
- **Why** (link the issue/ticket).
- **How to verify** (test steps, screenshots for UI changes).
- Checklist: tests added/updated, docs updated, breaking change called out.

## Draft PRs

Open a PR as draft the moment you have something worth discussing — don't wait until it's "done." This invites early design feedback and avoids wasted work. Convert to "Ready for review" when you want a real review pass (this also triggers CODEOWNERS review requests, which are **not** sent for draft PRs).

## Linking issues

Use closing keywords in the PR description so merging auto-closes the ticket:
```
Fixes #123
Closes #123
Resolves #123
```
