# minderhq/authoring — community plugin-authoring toolkit

Install-able Claude Code tooling for building **Minder product plugins**. This is the
public, community-facing companion to Minder's plugin ecosystem — it turns your Claude
into a guided plugin author that scaffolds, validates, and security-reviews a plugin
against the public [`plugin-sdk`](https://github.com/minderhq/plugin-sdk) and
[`plugin-template`](https://github.com/minderhq/plugin-template) before you open a PR to
[`plugins`](https://github.com/minderhq/plugins).

> **Naming:** "plugin" refers to a Minder **product** plugin (the thing you build).
> The Claude Code tooling here is prefixed `minder-` to stay distinct in a session's
> skill list.

## What's here

### `minder-authoring` (this marketplace's plugin)
A skill/command/agent bundle. It does **not** re-copy the SDK docs — it points at the
canonical public sources and adds guided authoring on top:

- **Skills** — `authoring-guide` (write a plugin against the SDK contract),
  `authoring-security` (the catalog's security discipline), `authoring-publish`
  (validate → refresh catalog → PR).
- **Commands** — `/minder-plugin-new` (scaffold), `/minder-plugin-check` (validate +
  lint + tests), `/minder-plugin-publish` (catalog + PR checklist).
- **Review agents** — `plugin-contract-reviewer` (protocol / SDK-contract / manifest
  correctness) and `plugin-security-reviewer` (SSRF, XML hardening, input-injection,
  secret handling, no-arbitrary-code).

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
