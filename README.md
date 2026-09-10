# minderhq/authoring — community plugin-authoring toolkit

Install-able Claude Code tooling for building **Minder product plugins**. This is the
public, community-facing companion to Minder's plugin ecosystem — it turns your Claude
into a guided plugin author that scaffolds, validates, and security-reviews a plugin
against the public [`plugin-sdk`](https://github.com/minderhq/plugin-sdk) and
[`plugin-template`](https://github.com/minderhq/plugin-template) before you open a PR to
[`plugins`](https://github.com/minderhq/plugins). The human-readable authoring
guides this toolkit automates live on the docs site:
[plugins](https://minderhq.github.io/docs/plugins/) ·
[authoring](https://minderhq.github.io/docs/plugins/authoring/) ·
[contract](https://minderhq.github.io/docs/plugins/contract/) ·
[publishing](https://minderhq.github.io/docs/plugins/publishing/).

> **Naming:** "plugin" refers to a Minder **product** plugin (the thing you build).
> The Claude Code tooling here is prefixed `minder-` to stay distinct in a session's
> skill list.

## What's here

This marketplace hosts **two plugins**, cleanly separated: `minder-authoring` (plugin
authoring + the MinderHQ dev policy) and `dev` (a generic, reusable autonomous
development operator).

### `minder-authoring`
A skill/command/agent bundle. It does **not** re-copy the SDK docs — it points at the
canonical public sources and adds guided authoring on top:

- **Skills** — `authoring-guide` (write a plugin against the SDK contract),
  `authoring-security` (the catalog's security discipline), `authoring-publish`
  (validate → refresh catalog → PR), and `minder-dev-policy` (the MinderHQ
  specialisation of the `dev` operator).
- **Commands** — `/minder-plugin-new` (scaffold), `/minder-plugin-check` (validate +
  lint + tests), `/minder-plugin-publish` (catalog + PR checklist), and
  `/minder-dev-queue` (start the MinderHQ development queue).
- **Agents** — `plugin-contract-reviewer` (protocol / SDK-contract / manifest
  correctness) and `plugin-security-reviewer` (SSRF, XML hardening, input-injection,
  secret handling, no-arbitrary-code).

### `dev` — the generic development operator
The autonomous development engine, **organization-agnostic** and installable on its own
for any GitHub repo/org:

- **Commands** — `/dev:develop` (start/continue the loop), `/dev:resume` (restore state
  and continue), `/dev:status` (read-only report).
- **Skills** — `dev-operator` (the engine: modes, loop, state, selection, recovery,
  safety), `dev-policy` (the adapter contract — schema + a worked non-MinderHQ example).
- **Agent** — `dev-implementer` (implements one issue end-to-end in its owning repo,
  following the active policy's conventions).

## The development operator

Work a GitHub issue backlog autonomously without re-pasting a workflow. The engine is
generic; a small **policy** specialises it per project. Architecture:

```
Generic Development Operator  (dev plugin: dev-operator + dev-implementer)
        ↓  reads
Repository / Organization policy  (dev-policy: a .dev/policy.yml or a policy skill)
        ↓  supplies
project rules · GitHub governance · conventions · validation · ADR/architecture source
```

- **Autonomous loop** — `load state → select next suitable issue → identify owning repo
  → inspect issue + code + instructions + dependencies → (consult architecture when
  significant) → implement → test → review → commit → push → open PR → verify → persist
  state → next`. No confirmation between ordinary issues.
- **Three modes for efficiency** — **RESUME** (restore git-backed state, continue from
  the last checkpoint; the default), **EXECUTION** (targeted work on the current issue),
  **DISCOVERY** (a backlog scan, only when the queue is unknown/exhausted). Not every
  issue starts with an org-wide scan.
- **Issue selection** — the engine surfaces priority, labels, issue type, milestones,
  dependencies, blocked state, readiness, duplicates, and existing work; the **policy**
  decides how to weigh them. It never blindly takes every open issue and never invents
  issues.
- **Repository awareness** — never assumes the current checkout owns an issue: it
  identifies `owner/repo#N`, verifies identity, reads that repo's instructions, and works
  in the correct repository. One issue = one focused PR; cross-repo only when required.
- **State / resume** — lightweight and git-backed. Either a committed `.dev/state.json`
  checkpoint (`current_issue`, `repository`, `phase`, `completed`, `failures`,
  `blockers`, `pr`, `next_action`, `last_checkpoint`) or pure re-derivation from open
  PRs/issues — the policy chooses. No database, no external state service.
- **Failure & recovery** — records failures, retries only when safe (never endlessly),
  persists blocked state and skips to another issue, never duplicates commits/PRs, and
  resumes cleanly after an interrupted session.
- **Safety** — never fabricates tests/PRs/commits, ignores acceptance criteria, bypasses
  security controls, exposes secrets, or modifies unrelated repos. Stops for genuine
  clarification, missing credentials/permissions, or contradictory requirements.

### Use it on any repo (non-MinderHQ example)
Install the `dev` plugin, drop a policy in your repo, and run `/dev:develop`:

```bash
claude plugin marketplace add minderhq/authoring
claude plugin install dev@minderhq-authoring
```
```yaml
# acme/widget/.dev/policy.yml
organization: acme
repositories: [acme/widget]
issue_selection: { ready_labels: [ready], blocked_labels: [blocked], priority: [priority:high, priority:low] }
branch: "{type}/{issue}-{slug}"
commit: conventional-commits
pr: { title: conventional-commits, base: main, reference: "Closes #{issue}" }
validation: ["npm run lint", "npm test"]
state: { mode: file, path: .dev/state.json }
```
No MinderHQ tooling, ADRs, or infrastructure required — see the `dev-policy` skill for
the full schema.

### Use it on MinderHQ
Install both plugins, then start the queue with a single short command:

```bash
claude plugin install dev@minderhq-authoring
claude plugin install minder-authoring@minderhq-authoring
```

Run **`/minder-dev-queue`** (or *"Start the MinderHQ development queue."*). It runs the
generic operator under `minder-dev-policy`, which maps MinderHQ's **public** governance
(native Issue Types; `priority:*`/`component:*`/`status:*` labels; Conventional-Commits;
PR templates) onto the policy schema, and **delegates** org-hygiene, ADR discipline,
resume, and CI-parity validation to the private Minder operator tooling (`minder-gh`,
`minder-adr`, `minder-resume`, `minder-dev`) **when it is installed** — reusing those
systems rather than duplicating them, and falling back to the generic built-ins when it
is not. Those operator commands are unchanged; nothing here modifies them.

## Install

```bash
claude plugin marketplace add minderhq/authoring
claude plugin install minder-authoring@minderhq-authoring   # plugin authoring
claude plugin install dev@minderhq-authoring                # the development operator
```

For authoring, from a checkout of your plugin (scaffolded from `plugin-template`) run
`/minder-plugin-new`, or ask Claude to review an existing plugin. For development, run
`/dev:develop` (any repo with a policy) or `/minder-dev-queue` (MinderHQ).

## The contract in one screen

A Minder plugin is a Python class the registry loads by the presence of an async
`register()` method (duck-typed — inheriting `PluginBase` is optional). The full
protocol lives in
[`plugin-sdk/src/minder_plugin_sdk/contract.py`](https://github.com/minderhq/plugin-sdk/blob/main/src/minder_plugin_sdk/contract.py);
the design rationale ("no arbitrary code, no plugin-supplied HTML; graceful
degradation") is
[`docs/rfc/0001`](https://github.com/minderhq/plugin-sdk/blob/main/docs/rfc/0001-extensible-plugin-contract.md).
The SDK ships the canonical validator — `minder-plugin validate <plugin>` — which this
toolkit's review agents mirror and extend.

## Public-safety boundary

This repo is **public**. Everything in it references only Minder's already-public
ecosystem (`plugin-sdk`, `plugin-template`, `plugins`, `docs`, `client`) by public
GitHub URL. It intentionally contains no internal infrastructure details, no
credentials, and no private-core specifics. Contributions must preserve that boundary.

## License

[Apache-2.0](./LICENSE), matching the rest of the public Minder ecosystem.
