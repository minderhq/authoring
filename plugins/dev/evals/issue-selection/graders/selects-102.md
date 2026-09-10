---
type: llm
criteria: >
  The response selects issue #102 as the next issue to implement. It rejects the others
  for the right reasons: #101 is lower priority; #103 is under-specified / not ready;
  #104 is already claimed (assignee + linked open PR); #105 is a duplicate. Selecting any
  issue other than #102, or choosing #103/#104/#105, is a fail.
---

Verifies the operator applies the generic selection order (priority → readiness) and
skips under-specified, claimed, and duplicate issues.
