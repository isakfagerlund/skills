---
name: ship-light
description: Take one small change from start to merge-ready PR without check-ins — understand, implement in the main thread, run the scoped checks, review only when the diff warrants it, open the PR, and report the preview link. Use this skill when the user runs /ship-light with a ticket ID, a ticket URL, or a plain description of a small change (a translation, a copy tweak, a config entry, a one-file fix). Use /ship instead for anything touching multiple subsystems, a migration, or business logic worth a full review.
argument-hint: "<ticket-id | ticket-url | short description of the change>"
disable-model-invocation: true
---

# Ship light

The low-ceremony sibling of `/ship`, for changes small enough that planning
documents, sub-agent fan-out and a Fable review cost more than they catch. Same
contract: the user has opted out of check-ins. **Do not** ask for approval between
phases, **do not** narrate progress in prose, **do not** ask "want me to
continue?". Work until the PR is open, then notify once.

Argument: a ticket ID (`COR-4919`, `UK-568`), a ticket URL, or a plain description
of the change.

## Is this the right skill?

Suited to: translations and copy, a static config entry, hiding or renaming a
hard-coded UI item, a one-file bug fix, a version bump, a doc change.

**Bail out to `/ship` and say so** the moment the work turns out to need a database
migration, a new endpoint, changes across more than ~3 files of real logic, or a
product decision. Escalating early is cheap; discovering it at review time is not.

## The only two reasons to interrupt

1. **Blocking question** — proceeding under any assumption would produce work that
   is unsafe or useless if the assumption is wrong. `PushNotification`, then
   `AskUserQuestion` with concrete options and a recommendation first.
2. **PR open** — one `PushNotification` with the PR and preview URL.

`PushNotification` takes `{status: "proactive", message: "<200 chars"}`. It is
suppressed when the user is at the terminal — a "not sent" result is expected, and
it is why the final message must repeat the link.

Ambiguity resolvable by reading code, git history, or the ticket is **not**
blocking — pick the reading a careful colleague would, note it in the PR body, and
continue.

## Phase 0 — Preflight

1. `TaskCreate` one task per phase, `TaskUpdate` exactly one to `in_progress` at a
   time. The task list is the progress report — do not also narrate in prose.
2. `git status`. Leave every pre-existing change alone; this checkout routinely has
   large untracked files at the root.
3. Confirm you are not on `main`. If you are, `git fetch origin` and branch from
   `origin/main` as `<author>/<ticket-slug>` — `get_issue` returns Linear's
   canonical branch name, so prefer that.
4. Skip `pnpm energy setup` and `pnpm db:start` unless the change actually needs the
   dev environment or the database. Most small changes do not.

## Phase 1 — Understand and locate

1. If given a ticket ID, `get_issue` plus `list_comments` — the real requirement is
   often in a comment. Find the Linear tools with `ToolSearch("linear issue
   comments")` if the MCP prefix differs on this machine. No ticket: work from the
   description and say so in the PR body.
2. Locate the code directly — `Grep`/`Glob` in the main thread. One `Explore`
   sub-agent only if the first two searches come up empty. No plan file, no
   scratchpad document: hold the change in the task list.
3. Read the narrowest docs entrypoint that applies (root `AGENTS.md`, then
   `apps/<app>/CLAUDE.md`), and check `.claude/skills/` for a skill covering this
   exact task — `icon-creator`, `schema-mapper` and friends still apply here.

## Phase 2 — Implement

Do it yourself in the main thread. No fan-out: coordinating a sub-agent costs more
than a small edit.

- Per `AGENTS.md`, trivial changes need no new tests — but run the existing
  type-check, lint and tests **scoped to what you touched**
  (`pnpm turbo check-types lint test -F @autarc/web`, `make build && make lint &&
  make test` for api-v2). Never the bare root scripts.
- Translations: new keys go into every locale except `id-ID` — it is Crowdin's
  in-context pseudo-language (`crwdns…` markers), not a real locale.
- Fix failures yourself. A red test is not a blocking question.
- Commit with a Conventional Commit subject. **Stage by explicit path — never
  `git add -A` or `git add .`.** Unrelated untracked root files would otherwise land
  in the PR and blow `PR Size Check`.

## Phase 3 — Review, only if it is worth it

Decide from the actual diff (`git diff origin/main...HEAD --stat` plus the diff
itself), not from the ticket's description of itself.

**Skip the review** when every one of these holds — say in the final message that
you skipped it and why:

- No changed control flow: no new conditional, loop, early return, or error path.
- No new file that contains logic.
- Nothing touching auth, payments, data access, migrations, or a plugin/API
  contract.
- Under roughly 50 changed lines outside tests and locale files.
- The scoped checks are green.

**Run one review agent** if any of those fails, or if you fixed something you were
not fully sure about:

```
Agent({
  subagent_type: "general-purpose",
  description: "Peer review small change",
  prompt: "<the intended outcome> ... Review `git diff <base>...HEAD` on two axes:
           (1) does it do what it says, (2) does it follow this repo's documented
           standards (read AGENTS.md and the relevant apps/<app>/CLAUDE.md).
           Findings ranked most-severe first, each with file:line and a concrete
           failure scenario. Say plainly if you find nothing. Do not edit files."
})
```

No `model` override — the session model is enough at this size; `/ship` is where
the Fable pass belongs. Wait for the completion notification.

For each finding: fix it, or write one line saying why it does not apply. Re-run
the scoped checks after fixing. **One round only** — a second round of severe
findings means this was not a `/ship-light` change; finish, and say so in the final
message.

Sub-agents see a smaller tool surface than you do: a reviewer reporting that a tool
"does not exist" is describing its own sandbox, not the repo.

## Phase 4 — Open the PR

1. `git push -u origin <branch>` **first** — `gh pr create` without an upstream
   prompts, and a prompt stalls the whole run.
2. `AGENTS.md` PR standards apply unchanged at every size: the whole template from
   disk verbatim via heredoc, Conventional Commits title, `Resolves <TICKET-ID>`,
   exactly one core preview label (`preview:web` unless the diff touches
   `apps/api/**`, Ory/Hydra config, or `apps/supabase/**`).
3. Ready for review, not a draft — `check-pr.yml` skips drafts, so a draft produces
   no CI to watch.
4. Watch CI in the background; `--watch` outlasts a foreground `Bash` call:

   ```bash
   gh pr checks <pr> --watch --fail-fast   # Bash with run_in_background: true
   ```

   **Cap the fix loop at two pushes.** Re-run a clearly unrelated flake once. After
   that, stop and report the failing check by name rather than grinding.

## Phase 5 — Notify

Check once for the preview comment — the preview jobs are part of the same
workflow, so green CI usually means the `Preview is ready!` comment already landed.
Pro web is `https://pr-<PR>-autarc-pro-staging.autarc.workers.dev`. The bot **edits
one comment in place**, so trust a `Preview is ready!` only once checks are green.

If it has not landed, wait with `Monitor` (not a foreground `Bash` call, capped at
10 minutes; not `ScheduleWakeup`, which only works in `/loop` dynamic mode):

```
Monitor({
  description: "preview deploy for PR <pr>",
  timeout_ms: 900000,
  persistent: false,
  command: `for _ in $(seq 30); do
  url=$(gh pr view <pr> --json comments --jq '.comments[].body' 2>/dev/null \
    | grep -oE 'https://pr-[0-9]+-autarc-pro-staging\\.autarc\\.workers\\.dev' | head -1)
  [ -n "$url" ] && echo "preview ready: $url" && exit 0
  sleep 30
done
echo "preview NOT ready after 15m"`,
})
```

A copy-only or translation change does not need the preview to resolve — report the
PR link and move on rather than burning 15 minutes on it.

One notification, then the final message:

```
PushNotification({
  status: "proactive",
  message: "COR-4919 ready: https://pr-1234-autarc-pro-staging.autarc.workers.dev — PR #1234, CI green"
})
```

Final message, in this order:

1. The PR link, and the preview link if it resolved.
2. What now works, in concrete terms.
3. Whether you ran the peer review or skipped it, and why.
4. One concrete next action.

## Guardrails

- Never merge. "Merge-ready" means open, green, labelled.
- Never force-push a shared branch. Never commit to `main`.
- Never `git add -A`/`git add .`, and never stage a file you did not change.
- Report faithfully: a skipped check or a still-failing test gets said out loud,
  with the output. Never claim green CI you did not observe.
