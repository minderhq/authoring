---
name: minder-dev-policy
description: The MinderHQ policy for the generic dev-operator — specialises autonomous development for the minderhq organization without duplicating MinderHQ's tooling. Use when running the development queue against minderhq repos ("Start the MinderHQ development queue", /minder-dev-queue). Maps MinderHQ's public governance (Issue Types, priority:*/component:*/status:* labels, Conventional-Commits, PR templates) onto the [[dev-policy]] schema and delegates org-hygiene, ADR discipline, and resume to the private Minder operator tooling when it is installed.
---

# minder-dev-policy — MinderHQ as a first-class dev-operator policy

This is a **policy skill**: it specialises the generic [[dev-operator]] for `minderhq`
by filling the [[dev-policy]] schema. It carries no engine of its own and **duplicates
nothing** — where MinderHQ already has tooling, this policy *delegates* to it. Requires
the `dev` plugin (the generic operator) installed; the delegated tooling below is
optional and the policy degrades gracefully when it is absent.

## Policy
```yaml
organization: minderhq
repositories: org:minderhq         # never assume the current checkout owns an issue
issue_selection:
  type_field:     native-issue-type          # Bug/Feature/Docs/Test/Security/Refactor/…
  priority:       [priority:p0, priority:p1, priority:p2, priority:p3]
  component_required: true                    # an issue needs ≥1 component:* to be ready
  blocked_labels: [status:blocked]
  skip_labels:    [status:triage, duplicate, wontfix]
  respect_dependencies: true
branch:  "{type}/{issue}-{slug}"
commit:  conventional-commits                 # feat/fix/docs/refactor/test/security/deps/chore
pr:
  title: conventional-commits
  base:  main
  template: .github/pull_request_template.md  # else the org default in minderhq/.github
  reference: "Closes #{issue}"
  requires: [component-label]                 # PRs also carry the affected component:*
github_automation: minder-operator            # see "Delegation" — never hand-roll gh reruns
architecture_source: minder-operator/minder-adr
validation: minder-operator/minder-dev        # CI-parity local loop; else the repo's own
safety:
  - public-safety boundary — no credentials, internal infra, or local paths in changes
  - never mass-rerun heavy CI across PRs (operator serialization); go PR-by-PR
state:
  mode: rederive                              # MinderHQ continuity is git-backed re-derivation
```

## How MinderHQ's signals are interpreted (public governance)
MinderHQ's governance is label-and-type driven and lives in the public `minderhq/.github`
(`labels.yml`, issue/PR templates):
- **Kind of work is not a label** — issues use a native **Issue Type**; PRs use a
  **Conventional-Commits** title prefix. Never recreate retired `type:*` labels.
- Label axes are `priority:*`, `component:*`, `status:*`. A ready issue has a priority
  and ≥1 component and is not `status:blocked`/`status:triage`. Order by
  `priority:p0…p3`, then milestone, then age.
- On PR: Conventional-Commits title + `component:*`; use the repo's `pull_request_template`
  (or the org default).

## Delegation — reuse, never duplicate
When the private **Minder operator** tooling is installed, the operator invokes it
instead of re-implementing it:
- **GitHub org-hygiene / merge / triage** → the operator's `minder-gh` governance
  (wraps the org-hygiene engine's `cycle`/`merge`/`sweep`). Never fire multi-PR CI
  reruns by hand — go through it PR-by-PR.
- **Architecture / ADRs** → the operator's `minder-adr` discipline (architecture ground
  truth is the private `minderhq/adrs` Status ledger). Consult **before**
  architecturally-significant work.
- **Resume / continuity** → the operator's `minder-resume` protocol: continuity is
  deterministic re-derivation from git-backed state (open PRs/issues + the ADR ledger),
  not a checkpoint blob — so `state.mode` is `rederive` here, not a `.dev/state.json`.
- **Validation** → the operator's `minder-dev` CI-parity loop for Minder services/client.

If that tooling is **not** present, fall back to the generic operator's built-ins: the
public GitHub governance above, the repo's own validation commands, and a
`.dev/state.json` checkpoint. Nothing here re-creates the operator's scripts, ADR store,
or infrastructure — this skill is policy only.

## Invocation
`/minder-dev-queue` (or "Start the MinderHQ development queue") runs the generic
[[dev-operator]] under this policy. Equivalent to `/dev:develop --policy minder-dev-policy`.
