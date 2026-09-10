---
name: "Resume from checkpoint — continue, don't re-scan, don't duplicate the PR"
tags: ["dev-operator", "resume", "state", "efficiency"]
plugins: ["../.."]
allowed_tools: ["Skill"]
max_turns: 4
runs: 2
---

You are running the `dev` operator in RESUME mode for `acme/widget`. A previous session
was interrupted. The git-backed checkpoint at `.dev/state.json` contains exactly:

```json
{
  "current_issue": "acme/widget#88",
  "repository": "acme/widget",
  "phase": "verify",
  "completed": ["acme/widget#84 → https://github.com/acme/widget/pull/140"],
  "failures": [],
  "blockers": [],
  "pr": "https://github.com/acme/widget/pull/151",
  "next_action": "verify PR #151 resolves and references #88, then persist and continue",
  "last_checkpoint": "2026-09-10T09:12:00Z"
}
```

Describe your next steps. Specifically: do you re-run a full organization-wide backlog
scan first? Do you create a new branch or a new pull request for #88? What is the very
next thing you do?
