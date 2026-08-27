---
name: ship
description: Deliver one small-to-medium ticket as a reviewed, merge-ready PR and wait for its preview.
argument-hint: "<ticket-id | ticket-url | pasted ticket text>"
disable-model-invocation: true
---

# Ship

Deliver one ticket without phase check-ins. Work until the PR is open, CI is
green, and the preview resolves. Never merge.

Interrupt only when a product decision or unsafe assumption blocks useful work.
Resolve everything else from the ticket, code, docs, and history. Record material
assumptions in the PR body.

## Model routing

Use sub-agents only for independent work with disjoint files. Give each agent the
ticket outcome, acceptance criteria, exact file ownership, relevant conventions,
and required checks. Include all needed context in its prompt.

- In Codex, choose between `gpt-5.6-luna` and `gpt-5.6-sol`. Use Luna for
  exploration and bounded changes that follow an existing pattern. Use Sol for
  business logic, API or data work, shared abstractions, unclear tasks, and any
  unit Luna fails to complete. Never use Terra.
- In Codex, review with `gpt-5.6-sol` at high reasoning effort.
- In Claude Code, use `opus` for exploration and implementation, then `fable` for
  review. Do not use another Claude model or use Fable for implementation.

## 1. Preflight

1. Read `git status` and preserve every existing change.
2. Work on a ticket branch, never `main`. Prefer the branch name returned by the
   issue tracker.
3. Read the applicable `AGENTS.md` and the narrowest app or package instructions.
4. Provision the worktree only when its required environment file is missing.

Complete this step when the branch, existing changes, applicable instructions,
and local environment state are known.

## 2. Understand

Fetch the ticket and its comments with the available issue-tracker tools. If that
fails, use the pasted ticket text and disclose the missing source in the PR.

Write down:

- the user-visible outcome;
- acceptance criteria;
- explicit exclusions;
- assumptions and what would disprove them.

Locate the entry points, data path, tests, and closest prior art. Check for a
repo-local skill that owns the task before inventing a workflow.

Stop for a blocking question if the acceptance criteria need a product decision
or the work no longer fits one small-to-medium PR. A schema migration combined
with cross-app changes is outside this skill.

Complete this step when every acceptance criterion maps to code and a test or
other observable check.

## 3. Plan

Keep the plan outside the repository. List the files, the change in each file,
tests to write first, verification commands, assumptions, and exclusions. Split
the work into units with disjoint file ownership. Files shared by two units stay
in the main thread.

Complete this step when every planned edit has one owner and one verification
method.

## 4. Implement

Write tests first where the repo requires them. Fan out independent units using
the model routing above.

Every implementation agent must follow this boundary:

> Edit only your assigned files. Run no git commands or shared environment,
> database, generation, or library-build commands. Report changed files and the
> checks you ran. The orchestrator integrates, validates, and commits.

Run shared commands and edit shared files in the main thread. After fan-in, inspect
the full diff and run the scoped type checks, lint, and tests required by the repo.
Fix failures yourself. Treat a Luna failure as a reason to retry that unit with
Sol, once.

Commit logical chunks with Conventional Commit subjects. Stage explicit paths
only.

Complete this step when the implementation meets every acceptance criterion and
all scoped checks pass.

## 5. Review

Run one review agent against the merge base using the review model above. Give it
the outcome and acceptance criteria. Ask it to check both spec compliance and
repository standards, rank findings by severity, cite `file:line`, and describe a
concrete failure case. It must not edit files.

Fix or explicitly reject every finding, then rerun scoped checks. Run one more
review after substantial fixes. Stop after two review rounds and ask a blocking
question if a severe finding remains.

Complete this step when the review reports no unresolved severe findings and the
checks remain green.

## 6. Open the PR

Push the branch without force, then create a ready-for-review PR using the
repository's current PR instructions and template. Include the ticket reference,
assumptions, test evidence, and exactly one applicable preview label.

Watch CI. Fix owned failures and retry an unrelated infrastructure failure once.
Stop after three fix pushes. Report any remaining failed check honestly. A PR size
failure means the ticket exceeded this skill; do not split it without permission.

Complete this step when the PR exists and CI is green, or the retry bound is
reached and the remaining failure is named.

## 7. Wait for preview

After CI is green, inspect the GitHub Actions comments for the current `Preview is
ready!` result. Ignore a preview that predates the latest push. Wait up to 30
minutes with the runtime's non-blocking monitor. Never invent or infer a preview
URL that was not observed.

The final response contains:

1. The preview and PR links, or the exact failed check or timeout.
2. What the user can now test.
3. Assumptions and exclusions.
4. One concrete next action.
