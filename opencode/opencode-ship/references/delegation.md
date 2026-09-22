# Delegation boundaries

Use only tools and agent roles exposed in this session. Prefer the native OpenCode
task tool with a fresh subagent for bounded reading or review. Supply outcome,
criteria, exact diff or revision, relevant conventions, and specific questions. Ask for findings and
provenance only. Do not pass the entire parent conversation or request nested
delegation. Use runner-configured role models; never invent model identifiers or
assume a task call can override the model.

Default to one implementation owner. Parallel implementation is appropriate only
when independent units justify the coordination and budget. Keep shared files,
environment changes, database commands, generators, and library builds with the
parent. Give each worker exact file ownership and required verification.

## OpenCode subagent

Confirm the exposed tool's workspace behavior. In a shared checkout, workers must
not run git mutations, environment setup, generation, or shared builds. Read-only
reviewers must not edit. The parent inspects the resulting diff and performs
integration and final checks. A configured read-only role is preferable to a
general role with broad permissions.

## Background-agents child session

Use this branch only when the user's current request explicitly asks for a child
session in a separate sandbox. Upstream forbids inferring that permission from a
request for subagents or suggesting a child session. Ordinary delegation uses the
native task tool. Follow the actual tool's restrictions if the fork differs.

`spawn-child` uses a separate sandbox and branch. It is not a shared-checkout
worker and inherits no conversation history. Before dispatch, establish how the
child receives the exact starting revision and any parent changes needed for its task. A reviewer must actually
have the target diff; a branch name without those commits is insufficient.

For an implementation child, require the base SHA, branch, committed head SHA,
changed files, and checks in its handoff. Permit its own scoped commits and push
only when needed for the agreed transfer mechanism. It must not open a competing
PR, merge, force-push, or mutate the parent's environment. The parent fetches or
receives the commits, verifies their base and scope, integrates them, and runs
checks on the combined result. Never assume the child's edits are already local.

Use child status/results through the exposed tool schema. No busy polling or
duplicate child creation after a timeout: inspect the existing child's state
first. If the transfer mechanism or exact revision cannot be established, keep
implementation local and report any unavailable required review.
