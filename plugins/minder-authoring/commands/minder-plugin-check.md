---
description: Validate a Minder plugin against the SDK contract and the catalog CI gates (validate + lint + mypy + tests) before publishing.
---

Run the same gates the catalog CI runs, in order, from a checkout with the plugin
added as `<name>/__init__.py`. Verify by running, not by reading:

```bash
pip install -e ".[dev]"
minder-plugin validate <name>/__init__.py    # SDK contract gate — must exit 0
black --check . && flake8 --max-line-length=100 --extend-ignore=E203,W503
mypy .
pytest -q                                     # all HTTP faked; no network
python scripts/gen_catalog.py --check         # fails if catalog.json is stale
```

Then run the review agents for depth beyond the mechanical gate:
- `plugin-contract-reviewer` — protocol / SDK-contract / manifest correctness.
- `plugin-security-reviewer` — SSRF, defusedxml, input allowlists, secrets, no-arb-code.

Any failure blocks publishing. See [[authoring-publish]] to ship once green.

$ARGUMENTS
