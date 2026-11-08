# Branching Strategies

How to choose a branching model and actually run it day to day.

## TL;DR decision guide

| Team size / situation | Recommended model |
|---|---|
| Small team, web app/service, continuous deploy | **GitHub Flow** |
| Large org, high commit volume, monorepo | **Trunk-Based Development (scaled)** |
| Versioned releases, desktop/mobile apps with app-store review cycles | **Git Flow** (or GitLab Flow with release branches) |
| Multiple environments (staging/prod) needing explicit promotion | **GitLab Flow (environment branches)** |

If you're not sure: **default to GitHub Flow or Trunk-Based Development.** Most teams that adopt Git Flow don't need its complexity and pay for it in merge pain.

---

## 1. GitHub Flow

Source: [docs.github.com/get-started/using-github/github-flow](https://docs.github.com/en/get-started/using-github/github-flow)

**Model:** `main` is always deployable. Everything happens on short-lived branches off `main`, merged back via PR.

**Steps:**
1. Create a branch off `main` with a descriptive name.
2. Commit changes locally, push regularly.
3. Open a PR early (even in draft) to start the conversation.
4. Request review once ready; address feedback.
5. Merge once approved and CI passes.
6. Deploy from `main` (or the merge commit) immediately or on your normal cadence.
7. Delete the branch.

**Best for:** teams that deploy continuously or on-demand (web services, internal tools, SaaS).

---

## 2. Trunk-Based Development (TBD)

Source: [trunkbaseddevelopment.com](https://trunkbaseddevelopment.com/)

**Model:** Everyone works off a single branch (`trunk`/`main`). Branches, if used at all, live **hours, not days**, and are strictly for code review before merging back — never used for long parallel development.

**Core claims from the source:**
- You should do TBD instead of Git Flow or other models with multiple long-running branches.
- Direct-to-trunk commits are fine for very small teams; larger teams use short-lived PR branches (still single-developer/single-workstation in scope) for review before merge.
- For bigger changes, use **feature flags** and **branch by abstraction** instead of a long-lived branch — ship incomplete work behind a flag, turn it on when ready.
- Core CI requirement: **every developer merges to trunk at least once every 24 hours.** This is what keeps the codebase continuously integrable.
- Release branches (if used) are cut from trunk just-in-time, hardened briefly, and **deleted after release** — they are not where ongoing development happens.
- Proof of scale: Google runs ~35,000 developers against a single trunk/monorepo this way.

**Best for:** larger orgs/monorepos, teams practicing true continuous integration, teams who want to eliminate merge hell entirely.

**Key technique — feature flags instead of branches:**
```
if (featureFlags.isEnabled("new-checkout-flow", user)) {
  return renderNewCheckout();
}
return renderLegacyCheckout();
```
Merge incomplete/untested code to trunk behind a flag that defaults off. This decouples **merge** from **release** — you can integrate continuously without exposing unfinished features to users.

---

## 3. Git Flow

Source: [Atlassian — Comparing workflows](https://www.atlassian.com/git/tutorials/comparing-workflows)

**Model:** Two permanent branches (`main` = production, `develop` = integration), plus supporting branches:
- `feature/*` — branched from `develop`, merged back into `develop`.
- `release/*` — branched from `develop` when preparing a release; only bugfixes land here; merged into both `main` and `develop`.
- `hotfix/*` — branched from `main` for urgent prod fixes; merged into both `main` and `develop`.

**Best for:** software with explicit, versioned releases and a review/certification step before shipping (e.g., mobile apps pending app-store approval, on-prem/shipped software, embedded firmware).

**Why to avoid it otherwise:** the extra branches and two-way merges (`release`→`main`+`develop`, `hotfix`→`main`+`develop`) create overhead and drift that most continuously-deployed teams don't need.

---

## 4. GitLab Flow

A middle ground between GitHub Flow and Git Flow. Two variants:
- **Environment branches:** `main` → `staging` → `production`, each environment a branch; code is promoted by merging forward.
- **Release branches:** like Git Flow's release branches, but simpler — no permanent `develop` branch; `main` is the single integration branch, and `release/x.y` branches are cut from it for stabilization.

**Best for:** teams that need an explicit, auditable promotion path across environments but don't want Git Flow's full ceremony.

---

## Branch naming conventions

Pick one and enforce it (e.g., via a pre-push hook or PR title check):

```
feature/<ticket-id>-short-description     e.g. feature/ENG-512-add-oauth-login
fix/<ticket-id>-short-description         e.g. fix/ENG-611-null-pointer-on-logout
chore/short-description                   e.g. chore/bump-webpack-5
spike/short-description                   e.g. spike/evaluate-grpc
hotfix/short-description                  e.g. hotfix/payment-gateway-timeout
```

## Rules of thumb regardless of model

- Branch from the latest `main`/trunk; rebase (`git pull --rebase`) frequently to avoid drift and painful merges.
- Keep branches **short-lived**: hours to a couple of days for TBD/GitHub Flow, no more than ~1 sprint for Git Flow feature branches.
- One logical change per branch. Don't bundle a refactor with a feature with a dependency bump.
- Protect `main`: require PR review + passing CI + no force-push (see [repo-and-branch-hygiene.md](./repo-and-branch-hygiene.md)).
