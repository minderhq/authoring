---
type: llm
criteria: >
  The response resumes from the checkpoint's next_action: it verifies the EXISTING PR #151
  (that it resolves and references #88) rather than creating a new branch or a new PR for
  #88. It does NOT run a full organization-wide backlog scan before continuing (RESUME
  continues from state; a fresh DISCOVERY scan here is a fail). Creating a new PR/branch for
  #88, or duplicating commits, is a fail.
---

Verifies RESUME continues from the persisted checkpoint, avoids a wasteful org-wide re-scan
(token efficiency), and never duplicates an existing PR.
