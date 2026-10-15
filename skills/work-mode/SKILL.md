---
name: work-mode
description: Main entry point for this skill collection. Choose and run the appropriate workflow using shared engineering principles and writing rules.
disable-model-invocation: true
---

# Work mode

Take the user's request, choose the workflow, and carry it through to the requested outcome. Keep this mode active for follow-up work in the session unless the user changes modes. Reassess the workflow when their intent changes.

## Principles

Before starting work, read [engineering-principles](../engineering-principles/SKILL.md) in full and follow its application and reporting rules. The principle definitions and their triggers live there.

## Non-negotiables

- **All prose.** Read [unslop](../unslop/SKILL.md) and apply it to all prose, including replies. When writing skills or other agent instructions, also read [writing-for-agents](../writing-for-agents/SKILL.md).
- **Before committing.** Read and apply [deslop](../deslop/SKILL.md) to the intended diff.
- **Automated review feedback.** When automated code-review or security feedback arrives, follow [babysit's feedback assessment](../babysit/SKILL.md#assess-feedback) within the current task's scope.
- **Broken skills.** When a skill breaks during the task, diagnose and repair it in a separate change and PR using [make-pr](../make-pr/SKILL.md). Keep the original task moving where possible, and report the repair rather than silently working around the failure. Respect the task's publication scope: for local-only or read-only work, report the defect and prepare only the repair that scope permits. If the skill's source is unavailable, report the failing instruction and the limitation.
- **Long or autonomous work.** For long, autonomous, or multi-phase work, or when the user will return later to review the result, read and apply [decision-trail](../decision-trail/SKILL.md). Keep its record alongside the selected workflow rather than replacing that workflow.
- **Workflow instructions.** Resolve all skill paths relative to this file. Read the selected workflow's full `SKILL.md` and follow its instructions directly. A workflow's manual-invocation setting does not require the user to invoke it again after choosing work-mode. Load other workflow files only when needed.

## Choose the workflow

Infer the intended outcome from the request and the session's existing authorization. Briefly state the chosen workflow and proceed. Ask only when missing information prevents a useful next step; for an ambiguous request, start with investigation.

- **Investigate or explain.** For a diagnosis, root-cause question, or investigation of a ticket, read [triage](../triage/SKILL.md). Answer ordinary explanations directly when they do not need a ticket investigation.
- **Implement or fix.** Read [ship](../ship/SKILL.md). Investigate as part of implementation, then verify and deliver the PR as that workflow specifies. If the user requests local edits only, stop after the requested edits and relevant checks. Use the current project's documented setup and launch commands when it does not provide `energy setup` and `energy launch`.
- **Implement and merge.** Read [lgtm](../lgtm/SKILL.md) when the user explicitly authorizes merging, or that authorization already exists for this task. A request to fix, implement, or ship alone uses `ship` and leaves merging to the user.
- **Verify existing work.** Read [verify](../verify/SKILL.md). For artifacts outside an app flow, inspect or exercise the actual artifact with the relevant checks rather than forcing a browser workflow.
- **Create or update a PR.** Read [make-pr](../make-pr/SKILL.md) for work already implemented. Preserve the requested scope, such as updating only an existing PR description. When `ship` or `lgtm` reaches this step, use their existing call to `make-pr`.
- **PR status or follow-up.** Read [babysit](../babysit/SKILL.md) for checking CI, conflicts, or review feedback, fixing PR problems, or monitoring an existing PR. Opening a PR alone does not select this workflow.
- **Write or edit.** Complete the requested writing with the shared writing rules. A document edit does not require the full shipping workflow unless the user asks for that delivery.
- **Agent handoff.** Read [agent-handoff](../agent-handoff/SKILL.md) when the user requests that workflow. Keep its task and model choices within the user's request.

For a task without a matching workflow, complete it directly using the shared rules and the user's scope. This mode does not require delegation or separate threads.

## Scope and transitions

Work-mode chooses how to perform the authorized task. It does not expand an investigation into implementation, a local edit into publication, or a PR into a merge. These scope rules govern routed workflows where their defaults would go further.

When the user asks to fix something already investigated, carry the findings into `ship` without repeating completed investigation. When a workflow calls another skill, treat its output as part of the same task and continue to the requested outcome.

## Report back

Give the result and the evidence required by the selected workflow, including unresolved issues or checks that could not run. Incorporate outputs from helper skills into one final reply. Follow the principle-reporting requirement in `engineering-principles`, even when a helper skill returns only paths or a PR URL.
