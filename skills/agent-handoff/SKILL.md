---
name: agent-handoff
description: Hand a task to other models in new T3 Code threads and bring their results back to this thread. Use when the user says "agent handoff", e.g. "agent handoff review with astra" or "agent handoff 3 astra sol opus review".
---

# Agent handoff

The user names a **task** and one or more **models**. You open one new top-level T3 thread per model, give each the same task, wait for them all, and bring one merged answer back here.

## 1. Read the request

`agent handoff [count] <models...> <task>`. Words can come in any order.

- **Models**: short names like `astra`, `sol`, `opus`, `sonnet`. Call `orchestrator_capabilities`, then match each name against the model ids and labels (ignore case). If several models match, pick the newest version: `sol` → `gpt-6.1-sol` over `gpt-6-sol`. If a name matches nothing, tell the user what is available and stop.
- **Effort**: use the model's default. Set it only if the user names one (`astra xhigh`).
- **Count**: if the user gives a number, it must equal the number of models named. If it doesn't (`3 review`, `3 astra review`), ask which models to use. Don't guess.
- **Task**: whatever is left, e.g. `review`, `research how auth tokens refresh`, or `plan the migration`. For `review`, the scope is everything on this branch compared with `main`: committed and uncommitted changes.

You have read the request when every thread has a resolved `providerInstanceId` + `model` and you can say the task in one sentence.

## 2. Write the brief

The new threads start empty and can't see this conversation. Write one brief that works on its own:

- The task, plus whatever context from this conversation it needs: goal, ticket, files, decisions already made.
- For a review: run `git diff main...HEAD` and `git status`, review that change, and report each finding with `file:line`, what is wrong, how it fails, and severity.
- End with: "Reply with your result as your final message."

The threads share this checkout and are allowed to edit files. If more than one thread will change files, ask the user before starting, because their edits will collide.

## 3. Open the threads

One `create_threads` call, with one entry per model:

- `title`: `<Task> · <Model label>`, e.g. `Review · GPT-6-Astra`
- `prompt`: the brief
- `target`: `{ providerInstanceId, model }`, plus `options` only when the user set an effort

Use `create_threads`, not `t3_thread_launch`. It keeps the threads on this branch and in this checkout. Give the user a link to each thread: `[title](t3-thread://v1/<threadId>)`.

## 4. Collect the results

Call `t3_thread_wait` on each thread. If it returns `timedOut: true`, call it again; the thread keeps working. When a thread is done, `t3_thread_read` it (`view: "messages"`) and take its last assistant message. Follow `nextTextOffset` until the whole message has been read.

You have collected the results when you hold every thread's final message, or you know why a thread failed.

## 5. Merge and report

For a **review**:

1. Group findings that describe the same problem.
2. Open the code and check each finding. Label it **confirmed**, **plausible**, or **wrong**, with one line saying why.
3. Report one list, most severe first. Each item gives `file:line`, the problem, your label, and which models flagged it. Drop the **wrong** ones into a short "Rejected" list at the end.

For **any other task**, report where the models agree, where they differ, and what you recommend.

Finish with the thread links so the user can open the full reports.
