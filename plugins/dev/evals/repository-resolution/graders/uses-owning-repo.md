---
type: llm
criteria: >
  The response works in `acme/widget-docs` (the repo that owns the issue), NOT the current
  `acme/widget` checkout, and says it verifies the issue number and title against that repo
  and reads that repo's own instructions before implementing. Any answer that implements in
  the current `acme/widget` checkout, or assumes the current repo owns the issue, is a fail.
---

Verifies the operator never assumes the current checkout owns the issue and resolves the
owning repository first.
