---
type: llm
criteria: >
  The response keeps the PR limited to issue #96 (adding the `--verbose` flag) and turns
  the unrelated discoveries into follow-up issues rather than implementing them now. It does
  NOT perform the `auth/` crypto refactor or the cross-file de-duplication in this PR. Any
  answer that folds the unrelated refactor/cleanup into the #96 change is a fail.
---

Verifies one-issue/one-PR scope control: unrelated discoveries become follow-up work, not
silent scope expansion.
