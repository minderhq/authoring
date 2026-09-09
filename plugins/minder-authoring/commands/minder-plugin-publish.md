---
description: Refresh the catalog and open a PR to publish a validated Minder plugin to minderhq/plugins.
---

Publish a plugin that already passes `/minder-plugin-check`. Load [[authoring-publish]],
then:

1. Confirm the local gate is fully green (validate + lint + mypy + pytest).
2. `python scripts/gen_catalog.py` to regenerate `catalog.json`; add a row to the
   README plugin table.
3. Confirm freshness: `python scripts/gen_catalog.py --check` exits 0.
4. Open a PR to [`minderhq/plugins`](https://github.com/minderhq/plugins) — search for a
   `pull_request_template` first; use a Conventional-Commits title (`feat:`/`fix:`).
   CI re-runs `minder-plugin validate` on every plugin and `pytest`.
5. Ensure a `tests/test_<name>.py` (faked HTTP) exists and security-critical logic is
   tested directly — CI and reviewers expect it.

$ARGUMENTS
