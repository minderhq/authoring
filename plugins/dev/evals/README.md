# dev-operator eval suite

A small `claude plugin eval` suite that pins the **decision contract** of the generic
[`dev`](../) operator — the runtime behaviours that are specified in markdown but would
otherwise never be checked. Each case presents a situation entirely in-prompt (or via a
tiny fixture), so cases are deterministic and offline (no live GitHub, no network).

Run (from the repo root; requires `claude plugin eval` early-access enabled):
```bash
claude plugin eval ./plugins/dev --json results.json --threshold 0.8
```

## Coverage map (review item → case)
| # | Runtime concern | Case |
|---|---|---|
| 1 | issue selection (priority/ready/skip) | `issue-selection` |
| 2 | repository resolution (owning repo, not cwd) | `repository-resolution` |
| 3 | policy resolution (`.dev/policy.yml` over defaults) | `policy-resolution` |
| 4 | state persistence (required fields) | `resume-no-duplicate` (fixture) |
| 5 | RESUME after interruption (continue, no re-scan) | `resume-no-duplicate` |
| 6 | blocked/dependent issues (skip) | `blocked-dependency` |
| 7 | dirty working tree (don't fold unrelated changes) | `dirty-tree-isolation` |
| 8 | failed implementation/test (no fabrication) | `no-fabricated-results` |
| 10 | duplicate work (don't recreate a PR) | `resume-no-duplicate` |
| 12 | one-issue/one-PR isolation (scope) | `scope-isolation` |
| 13 | token efficiency (RESUME avoids org-wide scan) | `resume-no-duplicate` (negative tool assert) |
| 14 | safety boundaries (no fabricated PR/commit/test) | `no-fabricated-results`, `scope-isolation` |

## What this suite deliberately does NOT cover
- **End-to-end execution** (#8/#9/#11 full path: implement → test → commit → push → PR →
  verify) needs a real repository and credentials; it is validated by real operator runs,
  not by offline evals. The cases here fix the *decisions* around those steps, not the
  git/GitHub side effects themselves.
- **Absolute token/context cost** is a design property, not a pass/fail assertion; it is
  proxied only where a wasteful action is observable (a broad re-scan during RESUME).
Structural invariants (skill/agent discovery, cross-links, no-leak, authoring unchanged)
are covered by `claude plugin validate` + the checks in the PR, not by this suite.
