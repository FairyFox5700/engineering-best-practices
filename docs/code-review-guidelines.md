# Code Review Guidelines

Synthesized from [Google's Engineering Practices](https://google.github.io/eng-practices/) and the [Microsoft Code-With Engineering Playbook](https://microsoft.github.io/code-with-engineering-playbook/code-reviews/).

## The standard to hold a review to

From Google's "Standard of Code Review":

> **In general, reviewers should favor approving a CL once it is in a state where it definitely improves the overall code health of the system, even if the CL isn't perfect.**

There is no such thing as perfect code, only *better* code. Don't block a change that is a net improvement just because it isn't flawless.

**Mandatory bar:** never approve a change that definitely *worsens* code health (bugs, broken tests, security holes, unreadable code) — the only exception is a declared emergency/incident fix, which should be followed up immediately.

**Principles for resolving disagreement:**
- Technical facts and data outrank opinions and preferences.
- Pure style (whitespace, etc.) is governed by the team's style guide/linter — not up for debate in review comments.
- Software design choices are *not* just style — they should be justified by engineering principles, not personal taste. If multiple valid approaches exist and the author can justify their choice, defer to the author.
- If no rule applies, ask for consistency with the existing codebase.
- If author and reviewer can't agree after discussion, escalate (tech lead, broader team discussion, code owner, manager) — **don't let a PR stall indefinitely over an unresolved disagreement.**

## What to actually look for

- **Design:** Does this change make sense in the system as a whole? Is this the right approach?
- **Functionality:** Does it do what the author intended? Are there edge cases, race conditions, or security implications?
- **Complexity:** Could this be simpler? Over-engineered code is a maintenance tax on everyone after you.
- **Tests:** Are there appropriate unit/integration tests? Do they actually test behavior, not just exercise lines?
- **Naming & readability:** Will someone unfamiliar with this change understand it in 6 months?
- **Comments:** Do comments explain *why*, not restate *what* the code already says?
- **Consistency:** Does it follow existing codebase/style conventions?
- **Documentation:** Are README/API docs/ADRs updated if behavior or interfaces changed?

## Giving feedback

- Prefix optional/non-blocking suggestions with **"Nit:"** so the author knows it's polish, not a blocker.
- Educational comments (teaching something new) are great — also prefix with "Nit:" or similar if not required for approval.
- Be specific and suggest an alternative, not just "this is wrong."
- Critique the code, not the person: "this function could..." not "you did this wrong."

## Review speed — Google's concrete SLA

Source: [Speed of Code Reviews](https://google.github.io/eng-practices/review/reviewer/speed.html)

- **Maximum response time: one business day.** If a review request comes in, respond by first thing the next morning at the latest.
- Don't interrupt focused coding time to do a review immediately — but respond at your next natural break point (after current task, after lunch, after a meeting).
- It's the **response** speed that matters most, not total time-to-merge — a quick "I'll get to this by EOD, in the meantime here's an initial pass" beats silence.
- Across time zones: try to respond while the author still has working hours left, or before they start the next day.
- **"LGTM with comments"** is explicitly encouraged when: you're confident the author will address the remaining comments, the comments are optional, or they're minor (typo, unused import, etc.) — don't block merge just to re-review trivial fixups.
- If a PR is too large to review in reasonable time, ask the author to split it (see [commits-and-pull-requests.md](./commits-and-pull-requests.md)) rather than sitting on it.

**Practical team SLA to adopt:**
- First response (even just acknowledgment) within **1 business day**.
- Full review turnaround within **2 business days** for normal-sized PRs.
- Urgent/hotfix PRs: same-day review, possibly via live pairing instead of async comments.

## Microsoft's Code-With Engineering Playbook — goals of review

Source: [microsoft.github.io/code-with-engineering-playbook/code-reviews](https://microsoft.github.io/code-with-engineering-playbook/code-reviews/)

Every PR (or check-in to a shared branch) should get a peer review. Goals:
- **Improve code quality** — catch defects before they land in shared branches.
- **Learn and grow** — reviewers and authors both get exposed to new patterns/approaches and can break bad habits.
- **Build shared understanding** of the codebase across the team (reduces bus factor).

## Approval etiquette

- Don't rubber-stamp large PRs just to "unblock" someone — push back on size instead (see PR size guidance).
- Use required reviewers / CODEOWNERS for files you own so review requests are automatic (see [documentation-practices.md](./documentation-practices.md)).
- At least one approval required before merge; two for changes to critical/shared infrastructure.
- Re-review after substantive changes; trivial fixups (typos, renames) don't need a second full pass if the reviewer already said "LGTM with comments."

## Conflict escalation path

1. Try to reach consensus directly in PR comments.
2. If stuck, have a quick synchronous conversation (call/huddle) — then **post the outcome back as a PR comment** for the record.
3. If still unresolved, escalate to tech lead / code owner / eng manager. Don't let the PR rot.
