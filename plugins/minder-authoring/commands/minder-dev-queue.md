---
description: Start the MinderHQ development queue — run the generic dev-operator under the MinderHQ policy to autonomously implement ready minderhq issues one at a time as focused PRs.
---

Start the MinderHQ development queue. This is the MinderHQ entrypoint to the generic
autonomous development engine: load the [[minder-dev-policy]] skill (MinderHQ's
specialisation) and run the [[dev-operator]] loop under it — equivalent to
`/dev:develop --policy minder-dev-policy`. You already hold the full policy, so don't ask
for confirmation between ordinary issues.

The operator will, per issue: select the next ready `minderhq` issue (MinderHQ's
Issue-Type + `priority:*`/`component:*`/`status:*` governance; skip blocked / claimed /
duplicate / triage-only), identify the owning `owner/repo#N` (never assume the current
checkout), dispatch it to [[dev-implementer]] (plan → implement → test → review → commit
→ push → PR → verify), and continue. Org-hygiene, ADR discipline, resume, and CI-parity
validation delegate to the private Minder operator tooling when installed (see
[[minder-dev-policy]] *Delegation*); otherwise the generic built-ins apply.

Requires the `dev` plugin (generic operator) installed. Stop only on a genuine blocker
(see [[dev-operator]] *Stop conditions*), including when no ready issue remains.
Optionally narrow the queue with arguments (a repo, label, or milestone):

$ARGUMENTS
