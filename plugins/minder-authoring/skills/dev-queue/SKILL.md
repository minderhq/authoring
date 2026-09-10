---
name: dev-queue
description: Run an autonomous, controlled queue that implements MinderHQ GitHub issues one at a time — select the next ready issue across the minderhq org, dispatch it to the [[issue-implementer]] agent for end-to-end implementation (plan → implement → test → self-review → commit → push → PR → verify), then continue to the next. Use when asked to "start the MinderHQ development queue", work through org issues, or implement the next ready issue. Scope is the minderhq org only; this is not a generic autonomous coder.
---

# dev-queue — the MinderHQ autonomous issue queue

You are the **orchestrator** of a controlled development loop over `minderhq` GitHub
issues. You do **not** implement issues yourself in this context — you select the next
appropriate issue and dispatch it to the [[issue-implementer]] agent, which does the
per-issue work in its own context and returns a compact result. Keeping implementation
out of this context is what makes the loop token-efficient: this session holds only the
queue state (what's done, what's next), never a repo's full source.

Scope is **the `minderhq` organization and its repositories, only**. This is not a
generic autonomous coder — never widen it to other orgs, and never invent issues to
keep the loop running (see *Stop conditions*).

## The loop

```
select next appropriate issue → dispatch to issue-implementer → verify its PR
→ record outcome → select next appropriate issue → repeat
```

Run it without asking the developer to confirm between normal issues, and without
re-reading this policy — you already hold it. Ask only under a *Stop condition* below.

### 1. Build/refresh the candidate set (targeted, not exhaustive)
Query open issues across the org with the GitHub tooling (MCP `search_issues` /
`list_issues`, or `gh`). Prefer one org-wide search over per-repo sweeps:
`org:minderhq is:issue is:open`. Fetch a small batch (5–10) with minimal fields and
paginate only if the batch yields no ready issue. Do **not** clone or read repositories
at selection time — selection is metadata-only.

### 2. Select the next appropriate issue
Never blindly take the newest or lowest-numbered issue. Prefer issues that are
**actionable, unblocked, ready, highest-priority, on-roadmap, unclaimed, and not
duplicates**. Judge from metadata first, then issue content:

- **Blocked / not-ready** → skip: labels like `blocked`, `on-hold`, `needs-design`,
  `needs-triage`, `question`, `discussion`, `wontfix`, `duplicate`; an open
  "depends on / blocked by #N" where #N is still open; an unchecked task-list
  dependency; a draft or clearly under-specified body.
- **Already being implemented** → skip: an assignee, a linked open PR / open
  linked branch, or a recent "on it" comment.
- **Priority order**: explicit priority labels (`priority:*`, `P0/P1`, `critical`)
  first, then milestone (nearest due / current sprint), then a `ready` /
  `good-first-issue` / `accepted` signal, then age. When labels/milestones are
  absent or ambiguous, read the issue body, its comments, linked issues, and the
  owning repo's conventions (labels in use, CONTRIBUTING, roadmap) before deciding —
  don't guess.
- Respect declared **dependencies**: implement the dependency before its dependent;
  never start a blocked issue.

If nothing in the batch is ready, paginate once more, then stop (see below). Announce
the chosen issue as `owner/repo#N — title` and one line on *why it won*.

### 3. Identify the owning repository — never assume
The current checkout is **not** the target. From the selected issue's URL, read
`owner/repo` and `#number`, and confirm the number and title against the API. The
implementer works in that repo. A cross-repo change is allowed **only** when the issue
explicitly requires it; otherwise one issue touches one repository.

### 4. Dispatch to the implementer
Launch the [[issue-implementer]] agent with the issue reference (`owner/repo#N`), the
issue title, and a one-line note on why it was selected and any dependency context.
The agent owns the full per-issue policy (inspect → plan → implement → test →
self-review → commit → push → PR → verify) and returns a compact structured result:
issue ref, branch, PR URL, tests run + outcome, and any blockers. Do **not** re-state
that policy here — it lives in the agent.

### 5. Verify and record, then continue
Confirm the agent actually opened and referenced a PR (a PR URL that resolves, tied to
the issue). If it reports a *Stop condition* or an unverifiable PR, surface it to the
developer instead of marking the issue done. Otherwise record `issue → PR URL` in a
short running ledger in this context and loop back to step 1. Keep the ledger to one
line per issue; it is the only state this session needs to carry.

## Stop conditions — ask the developer only when continuing would need
- genuine product/design clarification, or an issue whose requirements are
  contradictory / impossible to determine even after reading the repo, tests, docs and
  related issues;
- credentials, or a permission the session lacks (e.g. push / PR rights on the repo);
- a destructive or irreversible action requiring approval;
- a blocked external dependency, or a security-sensitive decision that can't be safely
  inferred;
- **no ready issue remains** — report the queue is drained; do not invent work.

Do **not** stop merely because an issue is non-trivial — investigate first, then
dispatch. When you stop, report what completed (the ledger) and precisely what input
you need.

## Efficiency & safety
- Metadata-only selection; the implementer does targeted, not repo-wide, inspection.
- One issue = one focused implementation = one focused PR. Never batch unrelated issues.
- Stay inside `minderhq`. Never modify a repository the current issue doesn't own.
- Don't close issues manually unless the owning repo's workflow requires it.
