---
type: llm
criteria: >
  The response selects #202 and does NOT start #201, because #201 is blocked by the
  still-open dependency #200. Choosing #201, or attempting to implement it, is a fail.
  Merely noting the dependency but still selecting #201 is a fail.
---

Verifies the operator respects a declared "Blocked by #N" dependency and never starts a
blocked issue.
