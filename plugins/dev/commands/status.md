---
description: Report autonomous-development status — the current issue, repository, phase, completed work, blockers, and next action — read-only, no changes.
---

Report the current development state **read-only** — make no changes, open no PRs. Load
the [[dev-operator]] skill for the state model, then:

1. Resolve the active [[dev-policy]] and read git-backed state (`.dev/state.json` or the
   policy's `state` source), plus a targeted view of open PRs/issues for the policy's
   repo scope.
2. Summarise concisely: **current issue** + repository + **phase**; **completed** (issue
   → PR); **failures** and **blockers** (with reason + what's needed); open **PR**; and
   the **next action** the loop would take.

Prefer the persisted ledger and a targeted query over an org-wide scan.

$ARGUMENTS
