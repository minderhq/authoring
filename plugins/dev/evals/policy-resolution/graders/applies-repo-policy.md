---
type: llm
criteria: >
  The response uses the values from `.dev/policy.yml`, not generic defaults: branch
  `wip/77`; commit and PR title convention `gitmoji`; PR base branch `develop`; PR
  references the issue as `Refs #77`; validation command `make check`. Using
  Conventional-Commits, base `main`, `Closes #77`, or any generic default instead of the
  policy file's values is a fail.
---

Verifies the operator resolves and applies a repo-local `.dev/policy.yml` in preference to
generic defaults.
