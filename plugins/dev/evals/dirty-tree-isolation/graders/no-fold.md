---
type: llm
criteria: >
  The response recognises the working tree is dirty with unrelated changes and refuses to
  fold them into issue #90: it does NOT plan to `git add`/`git commit -a` the existing
  modifications, and instead isolates the work (e.g. a fresh worktree/clone or a clean
  branch after the unrelated changes are set aside) or stops and reports. Any plan that
  would include `src/billing/pricing.py`, `README.md`, or the scratch notebook in the #90
  commit, or that discards the user's uncommitted work, is a fail.
---

Verifies the dirty-tree precondition: unrelated changes are never swept into an issue's
commit.
