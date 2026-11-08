# Security Basics for Repos

## Secret scanning

- Enable **GitHub secret scanning** (Settings → Code security → Secret scanning) on every repo — free for public repos, included in GHAS for private repos.
- Enable **push protection** so commits containing recognizable secret patterns (API keys, tokens, private keys) are **blocked before they ever land**, not just flagged after the fact.
- Never rely on scanning alone as your first line of defense — use `.gitignore` for credential files and environment-specific config, and prefer a secrets manager (Key Vault, AWS Secrets Manager, Vault) over committing anything sensitive, even temporarily.
- If a secret does leak into history: **rotate it immediately** — removing it from git history (`git filter-repo` / BFG) does not undo exposure if it was ever pushed; treat the credential as compromised the moment it's committed.

## Dependency scanning

- Enable **Dependabot alerts** (Settings → Code security → Dependabot alerts) to get notified of known vulnerabilities in your dependency tree.
- Enable **Dependabot security updates** to auto-open PRs that bump vulnerable dependencies to a patched version.
- Optionally enable **Dependabot version updates** (via `.github/dependabot.yml`) to keep dependencies current generally, not just for security patches — reduces the size of the eventual forced security-update jump.

Example `.github/dependabot.yml`:
```yaml
version: 2
updates:
  - package-ecosystem: "npm"
    directory: "/"
    schedule:
      interval: "weekly"
  - package-ecosystem: "github-actions"
    directory: "/"
    schedule:
      interval: "weekly"
```

## Signed commits

See [commits-and-pull-requests.md](./commits-and-pull-requests.md#signing-commits). Enforce via branch protection ("Require signed commits") where your org's compliance posture calls for it (SOC2, ISO 27001, supply-chain attestation requirements).

## Least-privilege tokens

- Scope Personal Access Tokens (PATs) and CI tokens to the **minimum permissions and repos needed** — never a blanket `repo` + `admin:org` token for a CI job that only needs to push to one repo.
- Prefer **GitHub Actions' built-in `GITHUB_TOKEN`** (automatically scoped to the triggering repo, short-lived) over a long-lived PAT wherever the workflow allows it.
- Prefer **fine-grained PATs** over classic PATs — fine-grained tokens can be scoped to specific repos and specific permissions (e.g., "contents: read" only).
- Rotate long-lived tokens/service-account credentials on a schedule (e.g., every 90 days) even if nothing is known to be compromised.
- Audit org-level and repo-level collaborator access periodically — remove access for people who've changed teams or left.

## Branch protection as a security control

Branch protection (see [repo-and-branch-hygiene.md](./repo-and-branch-hygiene.md)) is itself a security control: disallowing force-push and requiring reviews prevents a single compromised credential from silently rewriting `main` history or merging unreviewed code.

## Checklist

- [ ] Secret scanning + push protection enabled
- [ ] Dependabot alerts + security updates enabled
- [ ] `dependabot.yml` configured for version updates on key ecosystems
- [ ] Signed commits enforced where compliance requires it
- [ ] CI tokens scoped to minimum necessary permissions
- [ ] No long-lived classic PATs with broad scopes in active use
- [ ] Periodic access review scheduled (quarterly or better)
