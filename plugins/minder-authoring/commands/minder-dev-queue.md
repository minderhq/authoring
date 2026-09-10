---
description: Start the MinderHQ development queue — autonomously select and implement ready minderhq issues one at a time, each as a focused PR, without re-pasting the workflow.
---

Start the MinderHQ development queue. Load the [[dev-queue]] skill and run its loop —
you already hold the full policy, so don't ask the developer to re-state it between
issues.

1. **Select** the next appropriate `minderhq` issue from org-wide open issues
   (metadata-only; skip blocked / claimed / duplicate / not-ready; honour priority,
   milestones and dependencies).
2. **Identify** the owning `owner/repo#N` — never assume the current checkout is the
   target.
3. **Dispatch** it to the [[issue-implementer]] agent, which implements it end-to-end
   (plan → implement → test → self-review → commit → push → PR → verify) in that repo
   and returns a compact result.
4. **Verify** the PR exists, record `issue → PR`, and continue to the next issue.

Stay inside the `minderhq` org, one issue = one focused PR, and stop only on a genuine
blocker (see [[dev-queue]] *Stop conditions*) — including when no ready issue remains.
Optionally narrow the queue with arguments (a repo, label, or milestone):

$ARGUMENTS
