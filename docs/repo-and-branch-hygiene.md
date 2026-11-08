# Repo & Branch Hygiene

## Auto-delete merged branches

- **GitHub:** repo Settings → General → "Pull Requests" section → check **"Automatically delete head branches."** Enable this on every repo, no exceptions.
- **GitLab:** Settings → Merge requests → enable **"Delete source branch when merge request is accepted"** (and set it as the project default).

This alone eliminates most branch clutter — enable it before writing any manual cleanup scripts.

## Branch protection rules (GitHub)

Configure on `main` (Settings → Branches → Branch protection rules):
- ✅ Require a pull request before merging.
- ✅ Require approvals (minimum 1, recommend 2 for critical repos).
- ✅ Require review from Code Owners (see [documentation-practices.md](./documentation-practices.md)).
- ✅ Require status checks to pass before merging (build, lint, tests — see [ci-cd-practices.md](./ci-cd-practices.md)).
- ✅ Require branches to be up to date before merging.
- ✅ Require conversation resolution before merging.
- ✅ Require signed commits (if your org mandates it).
- ✅ Do not allow bypassing the above settings (applies rules to admins too).
- 🚫 Do not allow force pushes.
- 🚫 Do not allow deletions (of the protected branch itself).

## Stale branch pruning

Even with auto-delete-on-merge, branches accumulate from abandoned work, closed-without-merge PRs, or forks. Run a periodic audit.

**List branches already merged into `main` (safe to delete):**
```bash
git fetch origin
git branch -r --merged origin/main | grep -v 'main$' 
```

**Delete local branches already merged:**
```bash
git branch --merged main | grep -v "\* main" | xargs -r git branch -d
```

**Prune local references to remote branches that no longer exist:**
```bash
git fetch --prune
```

**Audit remote branches by last-commit age (flag anything >90 days untouched):**
```bash
for branch in $(git for-each-ref --format='%(refname:short)' refs/remotes/origin | grep -v 'HEAD$'); do
  last_commit_date=$(git log -1 --format="%ci" "$branch")
  echo "$last_commit_date  $branch"
done | sort
```

**Delete a specific stale remote branch (after confirming with its author):**
```bash
git push origin --delete feature/old-abandoned-work
```

### Automate it
Add a scheduled GitHub Actions workflow (or GitLab CI job) that runs monthly, lists branches with no commits in 90+ days and no open PR, and either:
- Posts a report to a Slack/Teams channel for manual triage, or
- Opens an issue tagging the last committer to confirm deletion.

Avoid fully automatic deletion of stale branches without human confirmation — someone might be mid-investigation on a long-paused branch.

## Repo archival policy

- If a repo has had no commits and no open issues/PRs for **12+ months** and is not a dependency of active projects, mark it read-only (GitHub: Settings → Archive this repository).
- Document *why* a repo was archived in its README before archiving (what replaced it, where functionality moved).
- Keep an index/README in your org profile or wiki listing archived repos and their replacements, so people don't waste time finding a dead end.

## Tags and releases cleanup

- Don't delete tags that correspond to published releases (breaks anyone referencing that version).
- Pre-release/RC tags (`v1.2.0-rc.1`) can be pruned once superseded by a stable release.
