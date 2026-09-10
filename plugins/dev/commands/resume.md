---
description: Resume autonomous development from git-backed state — restore where things stand and continue from the last checkpoint without a full re-scan.
---

Resume the autonomous development loop. Load the [[dev-operator]] skill and enter its
**RESUME** mode:

1. Resolve the active [[dev-policy]] and restore state — the checkpoint (`.dev/state.json`
   or the policy's `state` source) plus re-derivation from open PRs/issues (and the
   policy's architecture ledger, if any).
2. Report the restored position: current issue, repository, phase, completed work,
   failures, blockers, PR, next action.
3. Continue from `next_action` — finish an in-flight issue before selecting a new one;
   do **not** duplicate commits or PRs, and do **not** re-run a full org-wide DISCOVERY
   scan unless the queue is genuinely unknown.

Use this on a fresh session/machine, or when continuing another session's work.

$ARGUMENTS
