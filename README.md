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

### `minder-authoring` (this marketplace's plugin)
A skill/command/agent bundle with two capabilities — **plugin authoring** and the
**MinderHQ development queue**. It does **not** re-copy the SDK docs — it points at the
canonical public sources and adds guided authoring on top:

- **Skills** — `authoring-guide` (write a plugin against the SDK contract),
  `authoring-security` (the catalog's security discipline), `authoring-publish`
  (validate → refresh catalog → PR), and `dev-queue` (the autonomous issue-queue loop).
- **Commands** — `/minder-plugin-new` (scaffold), `/minder-plugin-check` (validate +
  lint + tests), `/minder-plugin-publish` (catalog + PR checklist), and
  `/minder-dev-queue` (start the development queue).
- **Agents** — `plugin-contract-reviewer` (protocol / SDK-contract / manifest
  correctness), `plugin-security-reviewer` (SSRF, XML hardening, input-injection,
  secret handling, no-arbitrary-code), and `issue-implementer` (implements one issue
  end-to-end in its owning repo).

## MinderHQ development queue

An additional, clearly-separated capability for working through `minderhq` GitHub issues
autonomously. Start it with **`/minder-dev-queue`** or just *"Start the MinderHQ
development queue."* — the workflow policy lives in the `dev-queue` skill, so you never
re-paste a long implementation prompt between issues.

- **What it does** — loops `select next ready issue → implement → test → self-review →
  commit → push → open PR → verify → next`, one issue at a time, each as a focused PR.
- **Issue selection** — org-wide, metadata-first, and *not* every open issue: it prefers
  actionable, unblocked, ready, highest-priority, on-roadmap issues and skips blocked,
  already-claimed, duplicate, or not-ready ones, honouring labels, milestones and
  dependencies. It never invents issues to keep the loop running.
- **Per-issue work** — each issue is dispatched to the `issue-implementer` agent, which
  identifies the **owning** repository (never assuming the current checkout), follows
  that repo's conventions and tests, self-reviews the full diff, and opens a PR that
  references the issue. One issue = one focused PR.
- **Autonomous loop** — no confirmation between normal issues. The orchestrator carries
  only a one-line-per-issue ledger; the agent does targeted (not repo-wide) inspection,
  keeping token/context usage low.
- **When it stops** — only on a genuine blocker: product/design clarification,
  missing credentials/permissions, an irreversible action needing approval, a blocked
  external dependency, a security-sensitive decision, contradictory requirements, or
  when no ready issue remains.
- **Scope limit** — the `minderhq` org and its repositories only. This is deliberately
  **not** a generic autonomous coding agent, and it never modifies a repository the
  current issue doesn't own.

## Install

```bash
claude plugin marketplace add minderhq/authoring
claude plugin install minder-authoring@minderhq-authoring
```

Then, from a checkout of your plugin (scaffolded from `plugin-template`), run
`/minder-plugin-new` to start, or ask Claude to review an existing plugin.

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
