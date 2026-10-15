---
name: verify
description: Check a change works by using it in a real running app and browser, and capture proof as screenshots or a recording. Use after implementing a feature or fix, before opening a PR, or when the user asks "does this actually work", "verify this", or "show me it working".
argument-hint: "[what changed and which flow it affects]"
---

# Verify

Prove the change works the way a user would see it, not just that tests pass.
Read [engineering-principles](../engineering-principles/SKILL.md) and apply the relevant verification principles when choosing checks and evaluating evidence.
Run the real app (the project's run skill or setup commands) and open it in a real browser; if the harness has a browser of its own (e.g. T3 Code's `preview_*` tools), use that, otherwise drive a local one.
Walk the affected flow from where a user starts it, with the inputs the ticket describes, and check the outcome on screen; also take the one or two neighbouring paths most likely to break, and read the console and network panel for new errors.
Capture evidence as you go: a screenshot for each state the user sees, and a short recording instead whenever the point is movement or sequence — animations, loading states, drag and drop, multi-step forms (T3 Code: `preview_recording_start` / `preview_recording_stop`).
When something is wrong, fix it within the authorized scope, then run the flow again from the start; keep only the evidence from the run where everything looked right. For an explicitly report-only request, preserve the failure evidence and report findings without applying fixes.
Save evidence outside the repo as `/tmp/pr-screenshots/<n>-<slug>.png` (or `.webm`), numbered in the order a user sees them, so `make-pr` can attach it.
Report what you ran, what you saw at each step, anything you could not verify and why, and the evidence paths in order; when another skill called you, that list is your whole answer.
