# OpenCode ticket workflows

Variants for OpenCode running in a fork of
[background-agents](https://github.com/ColeMurray/background-agents).
The original `skills/` directory is unchanged. These files define agent behavior;
they do not install models, permissions, automation hooks, or spending limits.

| Skill | Use |
| --- | --- |
| [opencode-triage](opencode-triage/SKILL.md) | Read-only diagnosis and a reusable decision handoff |
| [opencode-ship](opencode-ship/SKILL.md) | Authorized implementation, scoped checks, review, and PR delivery |

Ship includes a light path for static edits. There is no separate ship-light skill
and no dependency on the old `evidence`, `linear-context`, or `pr-simple` skills.
Their needed behavior lives in each skill's own references so managed imports
remain self-contained. Ship can handle a clear request without triage; install
both for cases needing diagnosis.

## Install

For managed skills, import each directory separately from this personal repository:

```text
opencode/opencode-triage
opencode/opencode-ship
```

Choose an explicit source commit and scope assignments to the target repository
or environment. Inspect the generated content and supporting files before saving.
Use a new session after re-importing changed skills; existing sessions keep their
pinned revision. Unique names avoid collisions with repo-local `triage` and `ship`.
Keep old workflow skills out of this automation's instructions/selection so their
unconditional telemetry or PR-rewrite policies do not run alongside these variants.
Check the effective skill catalog, including repo-local skills, before rollout.

For plain OpenCode, copy each directory into
`~/.config/opencode/skills/` or the target repository's `.opencode/skills/`, keeping
the directory names. `opencode/` in this personal repo is a source folder, not an
automatically discovered install location.

These skills use supported frontmatter only. Authorization belongs in the runner
and tool permissions; `disable-model-invocation` is not an OpenCode permission
boundary. Upstream behavior is documented in
[OpenCode skills](https://opencode.ai/docs/skills/) and
[managed skills](https://github.com/ColeMurray/background-agents/blob/main/docs/MANAGED_SKILLS.md).

## Invocation and handoff

For diagnosis: "Use opencode-triage for this ticket: ...". Persist its
`opencode-triage/v1` response with the source ticket version and repository SHA.
For delivery: "Use opencode-ship for this authorized ticket and handoff: ...".
Pass the handoff directly rather than starting a second investigation. A clear
copy edit can go straight to ship with its complete request.

The JSON response schemas in the skills are proposed integration contracts. The
runner must capture and supply them; upstream does not automatically interpret
these custom fields. `needs_input` and `blocked` stop dependent work. `waiting_ci`
means incomplete delivery. `no_change` requires evidence that no edit is needed.

## Runner responsibilities

- Configure supported provider/model IDs and reasoning settings outside the skills.
  Use a bounded reader role, an implementation role, and an independent read-only
  reviewer role. Measure model choices on representative tickets before routing
  by cost. Configure role permissions and iteration limits in the deployed runtime;
  prose instructions do not enforce them.
- Enforce per-ticket spend/time limits and a concurrency lock. Persist branch,
  PR, revisions, child IDs, retry counters, and returned state across restarts.
  Do not reset allowances when a session resumes.
- Pass the complete ticket snapshot, including available comments and transcripts,
  on the initial run. Persist the compact decision contract even when ship skips
  triage. Every new session receives that contract, the latest ship state, and any
  ticket deltas. Only follow-ups retaining the existing conversation may receive
  deltas alone. A workflow-triggered new session cannot recover omitted criteria
  from the ship status object.
- Use background-agents' `create-pull-request` tool and provide a working label-edit
  mechanism. The tool owns initial push and creation, but has no labels argument.
  Preserve the template body and ticket linkage. A returned manual creation link
  means no PR exists yet. Git credentials and PR API credentials can differ.
- Connect CI events to the saved PR and head SHA. Deduplicate events and reload
  current state before resuming. Upstream event automations can start sessions;
  wiring them to this continuation contract is a separate integration task.
- Provision reusable dependencies through the runner's lifecycle hooks/images.
  Keep per-session services ready without asking an agent to repeat setup on each
  phase. Repository setup requirements still apply.

OpenCode subagents and background-agents `spawn-child` are distinct delegation
mechanisms. A separate sandbox needs a revision/commit transfer. The ship reference
defines both cases without assuming shared files. Upstream permits `spawn-child`
only on an explicit current user request for child sessions; ordinary review uses
the native task tool. See
[upstream architecture](https://github.com/ColeMurray/background-agents/blob/main/docs/HOW_IT_WORKS.md),
[automations](https://github.com/ColeMurray/background-agents/blob/main/docs/AUTOMATIONS.md),
and [OpenCode agents](https://opencode.ai/docs/agents/).
The [PR tool](https://github.com/ColeMurray/background-agents/blob/main/packages/sandbox-runtime/src/sandbox_runtime/plugins/inspect-plugin.js),
[CLI wrapper](https://github.com/ColeMurray/background-agents/blob/main/packages/sandbox-runtime/src/sandbox_runtime/gh-wrapper.sh),
and [child tool](https://github.com/ColeMurray/background-agents/blob/main/packages/sandbox-runtime/src/sandbox_runtime/tools/spawn-child.js)
are the source for the runtime-specific restrictions above.

## Validate before enabling every ticket

Use past tickets in a disposable checkout and test PR destination. Include a copy
edit, deterministic bug without telemetry, screenshot-dependent request, backend
logic change, ambiguous request, stale handoff, failed CI, and retried delivery.
Check these outcomes:

- Complete supplied context is reused; a missing screenshot prompts a targeted read.
- A proven defect stays confirmed when production impact is unknown.
- Static changes skip unnecessary review; meaningful logic gets independent review.
- A stale or expired preview cannot produce `ready`; neither can missing checks.
- PR retries reuse the matching PR and preserve test evidence and human edits.
- Missing tools yield honest incomplete states rather than guessed results.
- Waiting can resume with the same counters and SHA, without restarting triage.

Record model usage, cached input, tool calls, agent count, elapsed time, retries,
and human corrections by phase. Compare cost per accepted PR and missed
requirements against the original workflows. Instruction size alone does not
measure savings. No live runner evaluation is included with these source files.
