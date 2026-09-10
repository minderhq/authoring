---
description: Start (or continue) autonomous development — select the next ready issue and implement it end-to-end as a focused PR, then continue, without re-pasting a workflow.
---

Run the autonomous development loop. Load the [[dev-operator]] skill and follow it — you
already hold the full policy, so don't ask for confirmation between ordinary issues.

1. **Load policy + state** — resolve the active [[dev-policy]] (argument → policy skill →
   `.dev/policy.yml` → repo-derived defaults) and restore git-backed state; prefer
   RESUME over a fresh org-wide scan.
2. **Select** the next suitable issue (priority / labels / type / milestone /
   dependencies / readiness; skip blocked, claimed, duplicate, under-specified; never
   invent issues).
3. **Identify** the owning `owner/repo#N` — never assume the current checkout.
4. **Dispatch** it to [[dev-implementer]] (plan → implement → test → review → commit →
   push → PR → verify).
5. **Persist** state and continue to the next issue.

Stop only on a genuine blocker (see [[dev-operator]] *Stop conditions*), including when
no ready issue remains. Optionally narrow the queue with arguments (a repo, label,
milestone, or `--policy <name>`):

$ARGUMENTS
