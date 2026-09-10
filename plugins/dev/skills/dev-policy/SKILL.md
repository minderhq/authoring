---
name: dev-policy
description: The lightweight adapter/policy contract that specialises the generic [[dev-operator]] for a specific project or organization. Use when configuring autonomous development for a repo/org, writing a project's dev policy, or understanding how MinderHQ ([[minder-dev-policy]]) plugs in. Defines the policy schema (org, repo scope, issue-selection interpretation, branch/commit/PR conventions, validation, architecture source, GitHub automation, safety, state) and how the operator resolves it. No framework, no code — a small declarative file or a policy skill.
---

# dev-policy — specialise the operator without forking it

A **policy** is the only thing that makes the generic [[dev-operator]] project-specific.
It is deliberately small: a declarative document (or a policy *skill* that a plugin
ships). It supplies *interpretation*; the operator supplies *mechanism*. There is no
framework and no abstraction layer to implement — you fill in fields.

## How the operator finds a policy
Resolution order (first match wins): explicit `--policy` argument → a **policy skill**
in the session (e.g. `minder-dev-policy`) → a repo file `.dev/policy.yml` (fallback
`.claude/dev-policy.yml`) → **generic defaults** derived from the repo's
`CLAUDE.md`/`AGENTS.md`/`CONTRIBUTING.md`, labels/milestones, and PR template.

## Schema (all fields optional; omitted → generic default)
```yaml
organization: acme                 # or a user; scopes issue search
repositories:                      # scope: an explicit allowlist or a pattern
  - acme/widget
  - acme/widget-docs
issue_selection:                   # how to WEIGH the signals the operator surfaces
  ready_labels:   [ready, accepted]
  blocked_labels: [blocked, on-hold, needs-design]
  skip_labels:    [duplicate, wontfix, question]
  priority:       [priority:p0, priority:p1, priority:p2]   # highest first
  type_field:     label            # label | native-issue-type
  require:        [milestone]      # signals an issue MUST have to be eligible
  respect_dependencies: true
branch:  "{type}/{issue}-{slug}"   # e.g. feat/123-add-widget
commit:  conventional-commits      # or a free-text convention description
pr:
  title: conventional-commits
  base:  main
  template: .github/pull_request_template.md
  reference: "Closes #{issue}"     # or "Refs #{issue}" when merge doesn't auto-close
github_automation: none            # or: name the tool/command the policy delegates
                                   # org-hygiene/merge to (e.g. an operator plugin)
architecture_source: none          # or: where ADRs/design live + when to consult
validation:                        # commands the implementer MUST run and report
  - "npm run lint"
  - "npm test"
safety:                            # restrictions that only ADD to the operator's gates
  - "never modify infra/ without an issue that names it"
state:
  mode: file                       # file | rederive
  path: .dev/state.json
```

## Worked example — a non-MinderHQ repo (`acme/widget`)
Drop this at `acme/widget/.dev/policy.yml`, then run `/dev:develop` from a checkout —
no MinderHQ tooling, ADRs, or infrastructure required:
```yaml
organization: acme
repositories: [acme/widget]
issue_selection:
  ready_labels:   [ready]
  blocked_labels: [blocked]
  skip_labels:    [duplicate, question]
  priority:       [priority:high, priority:medium, priority:low]
  respect_dependencies: true
branch: "{type}/{issue}-{slug}"
commit: conventional-commits
pr: { title: conventional-commits, base: main, reference: "Closes #{issue}" }
validation: ["npm run lint", "npm test"]
state: { mode: file, path: .dev/state.json }
```
The operator will: pick the highest-priority unblocked `ready` issue in `acme/widget`,
branch `feat/123-…`, implement + run the two validation commands (reporting real
output), open a Conventional-Commits PR that closes the issue, checkpoint to
`.dev/state.json`, and continue. This is the proof the engine is genuinely reusable.

## Writing a policy as a skill (richer integrations)
When a project needs to *delegate* to existing tooling (governance scripts, an ADR
store, a resume protocol) rather than just declare conventions, ship the policy as a
**skill** instead of a YAML file — it can reference other skills/commands and degrade
gracefully when they are absent. [[minder-dev-policy]] is the reference: it maps
MinderHQ's public governance onto this schema and delegates org-hygiene, ADR discipline,
and resume to the private Minder operator tooling **when installed**, without
duplicating it. Keep such skills to policy — never re-implement the tool they point at.
