# Documentation Practices

## README standards

Every repo's root `README.md` should answer, in order:
1. **What is this?** — one or two sentences, no jargon.
2. **How do I run it?** — setup/install/run instructions that actually work on a clean machine.
3. **How do I test it?**
4. **How is it structured?** — brief map of key directories/modules.
5. **Where do I go for more?** — links to deeper docs, architecture decisions, contribution guide.

Keep it short. Deep detail belongs in `docs/`, not a 2000-line README.

## CONTRIBUTING.md

Should cover:
- Branching model in use (link to your branching strategy doc).
- Commit message convention (e.g., Conventional Commits) if enforced.
- How to run tests/linters locally before opening a PR.
- PR size/description expectations.
- Code of conduct link, if public-facing.

## CODEOWNERS

Source: [GitHub docs — About code owners](https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/customizing-your-repository/about-code-owners)

Create `.github/CODEOWNERS` (GitHub checks `.github/`, root, then `docs/`, in that order, and uses the first one found):

```
# Default owners for everything
*                       @your-org/platform-team

# Frontend
/src/frontend/          @your-org/frontend-team

# Infra-as-code requires platform review
/infra/                 @your-org/platform-team

# A specific sensitive file
/src/auth/tokens.go     @alice @bob
```

**Behavior to know:**
- Code owners are **automatically requested for review** when a PR touches files they own.
- Code owners are **not** notified for **draft** PRs — only once marked "Ready for review."
- Owners must have write access to the repo (individual or visible team).
- You can require CODEOWNERS approval specifically via branch protection ("Require review from Code Owners"), on top of the general required-approvals count.
- Different branches can have different CODEOWNERS files — useful if e.g. a GitHub Pages branch is owned by a different team than `main`.

## Architecture Decision Records (ADRs)

Lightweight way to capture *why* a significant technical decision was made, so future engineers don't have to reverse-engineer intent from old Slack threads.

**Minimal ADR template** (save as `docs/adr/0001-use-postgres-over-dynamodb.md`):
```markdown
# 1. Use PostgreSQL instead of DynamoDB

## Status
Accepted

## Context
We need a primary datastore for the orders service. Team has strong
relational-query needs (joins across orders/customers/inventory) and
existing Postgres operational expertise.

## Decision
We will use PostgreSQL (RDS) as the primary datastore.

## Consequences
- Need to manage schema migrations explicitly (e.g., via Flyway/Prisma).
- Horizontal write scaling is harder than DynamoDB if volume grows 10x —
  revisit if we exceed ~5k writes/sec sustained.
- Gains: strong consistency, ad-hoc querying, mature tooling/familiarity.
```

**Rules of thumb:**
- Number ADRs sequentially, never renumber or delete — supersede instead (`Status: Superseded by ADR-0007`).
- Write one when a decision is (a) hard to reverse, (b) affects multiple teams, or (c) you expect someone to ask "wait, why did we do it this way?" in 6 months.
- Keep them short — a page, not a design doc. The goal is *context capture*, not exhaustive specification.
- Store them in-repo (`docs/adr/`) so they version alongside the code they justify.

## Keeping docs from rotting

- Treat doc updates as part of the definition of done for a PR that changes behavior, config, or public interfaces — not a follow-up task.
- Link docs from the README so they're discoverable, not orphaned in a wiki no one remembers exists.
- Periodically (quarterly) sweep for docs referencing deprecated/removed functionality.
