---
name: babysit
description: Check or follow up on an existing PR's CI, conflicts, and review feedback. Use for PR-status requests, getting a PR green, addressing review comments, or monitoring a PR. Opening a PR alone does not trigger this skill.
---

# Babysit

Identify the PR from the request or current branch. State the mode implied by the request: report status, fix problems, or monitor. Ask for the PR only when it cannot be determined unambiguously.

Inspect CI checks, merge conflicts, and unresolved review feedback. For a status request, report the findings without making changes. For a fix request, assess feedback as described below, fix confirmed problems within scope, run relevant checks, and push the corrections. Follow [engineering-principles](../engineering-principles/SKILL.md), and apply [deslop](../deslop/SKILL.md) before committing.

For monitoring or waiting on checks after fixes, use the host's PR watcher when available. In T3 Code, register the PR with `link_pull_request`, call `watch_pull_request`, and yield until notified instead of polling. On notification, continue in the agreed mode. Call `unwatch_pull_request` when handing the work back. If persistent monitoring is unavailable, report the current state and that limitation.

Report the PR link, changes made, checks, and remaining blockers. This workflow does not authorize merging. If the user requests a merge, treat it as a separate authorized action after verifying readiness.

## Assess feedback

Check review findings, including automated code-review and security findings, against the actual code and requirements. Fix confirmed issues within the authorized scope. Give concrete evidence when a finding is incorrect or does not warrant a change. Report uncertainty and what remains to establish it when the evidence cannot settle a finding.
