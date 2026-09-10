---
name: "Blocked / dependent issue is skipped"
tags: ["dev-operator", "selection", "dependencies"]
plugins: ["../.."]
allowed_tools: ["Skill"]
max_turns: 4
runs: 2
---

You are running the `dev` operator for `acme/widget` under generic defaults. Do not call
GitHub or run anything — decide from the backlog below.

Open issues:

- #201 "Migrate storage to v2 schema" — labels: `priority:high`, `ready`; body says
  "**Blocked by #200** — cannot start until the v2 schema (#200) lands." #200 is still open.
- #202 "Add retry to the uploader" — labels: `priority:medium`, `ready`,
  `component:api`; no dependencies; no assignee; no linked PR.

Which issue do you select next, and what do you do about the other one? Explain briefly.
