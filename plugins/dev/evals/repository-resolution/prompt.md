---
name: "Repository resolution — owning repo, not the current checkout"
tags: ["dev-operator", "repository"]
plugins: ["../.."]
allowed_tools: ["Skill"]
max_turns: 4
runs: 2
---

You are running the `dev` operator. Your current working directory is a checkout of
`acme/widget`. The next selected issue is:

    acme/widget-docs#312 — "Document the new CSV export endpoint"

Before implementing, what repository do you work in, and what do you verify first? Do not
run anything — describe the steps. In particular, state whether you would make the change
in the current `acme/widget` checkout.
