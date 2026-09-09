---
name: authoring-publish
description: How to validate and publish a Minder plugin to the public catalog. Use when a plugin is ready to ship — running the SDK validator, refreshing catalog.json, and opening a PR to minderhq/plugins. Follows the catalog's CI gates so a green local run predicts a green PR. Pair with [[authoring-guide]] and [[authoring-security]].
---

# authoring-publish — validate and ship to the catalog

**Canonical reference (read, don't duplicate):**
[`plugins/CONTRIBUTING.md`](https://github.com/minderhq/plugins/blob/main/CONTRIBUTING.md)
and `scripts/gen_catalog.py` in the
[`plugins`](https://github.com/minderhq/plugins) repo.

## Local gate — mirror the catalog CI, in order
From a checkout of the `plugins` repo with your plugin added as `<name>/__init__.py`:
```bash
pip install -e ".[dev]"
minder-plugin validate <name>/__init__.py    # SDK contract gate — must exit 0
black --check . && flake8 --max-line-length=100 --extend-ignore=E203,W503
mypy .                                        # the SDK ships py.typed
pytest -q                                     # all HTTP faked; no network
python scripts/gen_catalog.py --check         # fails if catalog.json is stale
```

## Publish steps
1. Scaffold via `plugin-template` or `minder-plugin scaffold <name>`; add as a package
   `<name>/__init__.py` importing from `minder_plugin_sdk`.
2. Make the local gate above fully green.
3. `python scripts/gen_catalog.py` to refresh `catalog.json`, then add a row to the
   README plugin table.
4. Open a PR to `minderhq/plugins`. CI re-runs `minder-plugin validate` on every plugin
   and `pytest` (a test fails if `catalog.json` is stale).

## Non-negotiables
- A `tests/test_<name>.py` with faked HTTP must exist; security-critical logic tested
  directly (see [[authoring-security]]).
- `__all__` set (or a single class with `register`) so the loader and `gen_catalog`
  can discover the class.
- Run the review agents (`plugin-contract-reviewer`, `plugin-security-reviewer`) before
  opening the PR.
