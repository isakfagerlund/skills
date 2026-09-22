# PR and completion

## One PR owner

In background-agents, commit first, then use the exposed `create-pull-request`
tool. It handles push and PR creation through the control plane. Pass the exact
title, body, and intended base through its actual schema. The current branch must
be the verified ticket branch. Upstream blocks `gh pr create` and `gh pr new`;
never bypass that wrapper. If the tool is absent, return `blocked` rather than
inventing an alternate creation flow. A fork's explicit replacement contract
takes precedence.

The upstream PR tool has no labels argument. Verify access to an allowed GitHub
label-edit mechanism before creation, then apply the repository's required core
and app labels immediately after creation and read them back. Other `gh` commands
may have brokered credentials; verify access without printing tokens. If required
labels cannot be applied, return `blocked`, preserving any PR already created.
Do not assume git push credentials also authorize GitHub API operations.

The tool can return a manual creation URL after pushing a branch. That is not an
existing PR: return `needs_input` with the manual action, leaving `pr_url` null.
Only an observed PR number/URL and matching head/base establish creation.

Before mutation, look up an existing PR for the exact repository, ticket branch,
and base. Resume a matching PR. A closed/merged PR or mismatched scope requires
reconciliation, not blind creation. A network timeout after creation requires a
lookup before retrying. Runner locking is needed to prevent simultaneous creators;
a prompt-level lookup alone cannot guarantee that.

Read the current repository PR template and rules. Compose the final body once:
problem, resulting behavior, ticket reference, assumptions, exclusions, and actual
test/review evidence. Preserve the required template sections and checklist state.
Put review evidence in Description if no dedicated section exists. Use a temp
body file or structured argument to preserve newlines and literal content.

Let the creation tool push the owned branch; do not add a redundant initial push.
For later updates, use the permitted branch push mechanism without force. Create
a ready-for-review PR when repository CI requires it; verify its actual draft
state and labels rather than assuming tool defaults.
Do not run a second automatic PR simplification pass. If higher-priority rules
require one, preserve every substantive assumption, exclusion, check result, and
screenshot through that edit. Update an existing description only when facts
changed, preserving human additions. Handle and resolve review threads according
to the repository's rules.

## Observe the delivered revision

Read the PR's current head SHA. Inspect its required checks and applicable workflow
runs. All expected checks must have reported; an empty or partially registered
check list is pending, not green. Treat skipped required validation as missing
unless the repository explicitly allows that skip. Record run/job IDs and SHA.

For preview, require a successful applicable deployment tied to that head SHA
and an observed URL from its deployment metadata, job output, or trusted bot
comment. A URL match or comment timestamp alone proves neither deployment nor
freshness. When a bot edits one comment in place, correlate its update with the
successful deployment run for this head. Verify the intended app, not only a
default web URL. Check reachability if the repository requires it; HTTP 200 alone
does not prove freshness. An authenticated preview may require browser evidence.

If CI uses a synthetic merge SHA, verify its documented association with this PR
head and base. Recheck the head before reporting success. If it changed, invalidate
the old completion evidence and inspect the new revision. No preview is needed
only when repository requirements and this task explicitly allow that omission;
record the reason.

## Wait without repeated reasoning

When a configured runner continuation exists, return `waiting_ci` with PR/head,
observed run IDs, counters, and the next check. The runner persists state and
resumes after the matching completion event. An upstream workflow trigger does
not by itself promise to resume this session or carry this state.

Without configured continuation, use an available bounded process/watch facility
for at most ten minutes total, reading its output when it finishes. Do not invent
Claude-specific monitor tools or spend model turns repeatedly inspecting unchanged
checks. If the runtime cannot wait, or the deadline expires, return `waiting_ci`
and say that a follow-up invocation is required. Do not claim a callback was armed.

Fix owned failures within the ship retry budget; rerun a clearly unrelated
infrastructure failure once. Remaining failures return `checks_failed` with the
specific job and evidence. Required unavailable validation returns `blocked`.
Only current, complete evidence permits `ready`.
