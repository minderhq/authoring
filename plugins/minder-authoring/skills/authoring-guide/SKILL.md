---
name: authoring-guide
description: How to author a Minder product plugin against the public plugin-sdk. Use when writing, scaffolding, or explaining a Minder plugin — implementing register()/collect_data()/AI_TOOLS, choosing a code plugin vs a declarative manifest, or wiring CONFIG_SCHEMA/ACTIONS/REQUIRES/DISPLAY. Points at the canonical SDK contract; pair with [[authoring-security]] before opening a PR.
---

# authoring-guide — write a plugin against the SDK contract

**Canonical references (read, don't duplicate):** the SDK contract
[`plugin-sdk/src/minder_plugin_sdk/contract.py`](https://github.com/minderhq/plugin-sdk/blob/main/src/minder_plugin_sdk/contract.py),
the design RFC
[`plugin-sdk/docs/rfc/0001`](https://github.com/minderhq/plugin-sdk/blob/main/docs/rfc/0001-extensible-plugin-contract.md),
and [`plugin-template`](https://github.com/minderhq/plugin-template).

## Two plugin shapes — pick first
- **Code plugin** (Python class): `apiVersion` is `minder.dev/v1`. Start from
  `plugin-template`'s `plugin.py`. Use this whenever you fetch/compute anything.
- **Manifest plugin** (declarative YAML, `webhook → store-vector`, **no code**):
  `apiVersion` is `minder.dev/v1alpha1`, validated against the SDK's
  `schemas/manifest.schema.json`. Use for a pure webhook-ingest pipeline.

## The contract (code plugin)
The registry loads a class by the presence of `register()` — inheriting `PluginBase`
is optional (duck-typed).
- **`async register() -> PluginMetadata`** — the ONLY hard requirement. `name`,
  `version` (semver), `description`, `author` must be non-empty.
- **`async health_check() -> {"healthy": bool, ...}`** — the monitor reads
  `health["healthy"]`; the key is mandatory. Only healthy after `initialize()`.
- **`async collect_data()` / `analyze()`** return dicts. `collect_data` must **never
  raise** — catch/log and return `[]`/`None` (fail-soft; don't crash the loop).

## Optional surfaces (class attributes)
- `CONFIG_SCHEMA` — flat field list (`key/type/default/description`, `secret: true`
  for credentials). `ACTIONS` (frozenset of callable method names) + optional
  `READ_ONLY_ACTIONS`. `AI_TOOLS` — each `action` MUST be in `ACTIONS`; `parameters`
  is a JSON-Schema object. `REQUIRES` — only `KNOWN_SERVICES`/`KNOWN_BUNDLES`.
  `DISPLAY` — `logo` is a **lucide icon name**; a plugin never ships HTML.

## Non-negotiables
- **No tier/license field exists** in the contract — don't invent one. The nearest
  classifier is `DISPLAY.category`; licensing is your repo's `pyproject.toml`.
- Validate with the SDK's own gate: `minder-plugin validate <plugin>` and keep tests
  green. Then run [[authoring-security]], then [[authoring-publish]].
