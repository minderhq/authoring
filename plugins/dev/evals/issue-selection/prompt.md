---
name: "Issue selection — highest-priority ready, skip the rest"
tags: ["dev-operator", "selection"]
plugins: ["../.."]
allowed_tools: ["Skill"]
max_turns: 4
runs: 2
---

You are running the `dev` operator's development loop for the `acme/widget` repository
under generic defaults (no custom policy). Do **not** call GitHub or run anything — decide
from the backlog below and state your choice.

Open issues (metadata only):

- #101 "Add CSV export" — labels: `priority:low`, `ready`; no assignee; no linked PR.
- #102 "Fix crash on empty upload" — labels: `priority:high`, `ready`, `component:api`;
  no assignee; no linked PR.
- #103 "Rework the whole config system" — labels: `priority:high`; body: two sentences,
  no acceptance criteria; no assignee.
- #104 "Investigate flaky test" — labels: `priority:high`, `ready`; assignee: @dana;
  linked open PR #150.
- #105 "Add CSV export" — labels: `priority:high`, `ready`; marked `duplicate` of #101.

Which single issue do you select next, and why? Name the issue number and give a one-line
justification. Then briefly say why each of the others was not chosen.
