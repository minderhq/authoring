---
name: "Policy resolution — repo .dev/policy.yml wins over generic defaults"
tags: ["dev-operator", "policy"]
plugins: ["../.."]
allowed_tools: ["Skill"]
max_turns: 4
runs: 2
---

You are running the `dev` operator for `acme/widget`. No `--policy` argument was given
and no policy skill is loaded, but the repository contains `.dev/policy.yml` with exactly
this content:

```yaml
organization: acme
repositories: [acme/widget]
branch: "wip/{issue}"
commit: gitmoji
pr: { title: gitmoji, base: develop, reference: "Refs #{issue}" }
validation: ["make check"]
```

You are about to implement issue #77 "Add pagination". State, for this issue:
1. which branch name you will use,
2. which commit/PR-title convention you will follow,
3. which base branch the PR targets,
4. how the PR references the issue, and
5. what validation command you will run.

Do not run anything — just state the values you will use and where they come from.
