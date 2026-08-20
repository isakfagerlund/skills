# skills

Collection of [Claude Code](https://claude.com/claude-code) skills I use, packaged as a plugin marketplace.

## Skills

| Skill | Invocation | What it does |
| --- | --- | --- |
| [`triage`](skills/triage/SKILL.md) | `/triage [ticket, URL, or error signal]` or model-invoked | Understands a work item, challenges it with production data, and returns findings, options with tradeoffs, and a plan brief. Analysis only — never implements. |
| [`ship`](skills/ship/SKILL.md) | `/ship <ticket-id \| ticket-url \| pasted ticket text>` | Takes one small-to-medium ticket from start to merge-ready PR without check-ins: understand, plan, implement with sub-agents, review with a Fable sub-agent, handle the feedback, open the PR, wait for the preview link. |
| [`ship-light`](skills/ship-light/SKILL.md) | `/ship-light <ticket-id \| ticket-url \| short description>` | The low-ceremony `ship`, for changes too small to earn the ceremony — a translation, a copy tweak, a config entry, a one-file fix. No plan file, no sub-agent fan-out, no Fable review; a peer review only when the diff actually warrants one. |
| [`pr-simple`](skills/pr-simple/SKILL.md) | `/pr-simple` or model-invoked | Rewrites the current branch's PR description so anyone can understand it — short, visual (mermaid diagram or before/after table), simple technical English — and pushes it with `gh pr edit`. |
| [`writing-for-agents`](skills/writing-for-agents/SKILL.md) | model-invoked | Reference for writing any document an agent consumes — a skill, an `AGENTS.md` / `CLAUDE.md`, a doc reached by a pointer: context pointers, the information hierarchy, completion criteria, leading words, pruning. |
| [`bro`](skills/bro/SKILL.md) | `/bro` or model-invoked | Re-explains the previous assistant message in plain language, for when the reply didn't land. Re-expresses only — never re-answers, never adds information, and every path, command and number survives verbatim. |
| [`evidence`](skills/evidence/SKILL.md) | model-invoked (used by `triage`) | Challenges a load-bearing claim with production data: falsifiable predictions, an evidence ledger, the kill query, and a no-fabrication guard. |
| [`unslop`](skills/unslop/SKILL.md) | model-invoked | Edits text to strip AI tells — puffery, AI vocabulary, "not just X but Y", em dashes, boldface and inline-header lists, chatbot phrases, filler, abstract metaphor nouns — and to put voice back in. Vendored from [`cursor/plugins`](https://github.com/cursor/plugins/tree/fd6dd6f7276956a532bb78a748a8d2818b6eb5f4/pstack/skills/unslop), unmodified. |

`triage` loads `evidence`, so keep them installed together. `writing-for-agents` loads its own
[`SKILL-MECHANICS.md`](skills/writing-for-agents/SKILL-MECHANICS.md) when the document being written is a skill.

`unslop` is a third-party skill, copied verbatim so it stays diffable against upstream. Two things to know before
installing it: its description says "Must always apply", so it loads on every turn rather than firing on a trigger,
and rule 13 (no em dashes) plus rule 26 (abstract metaphor nouns, including `harness`, `primitive` and `surface`)
contradict how `writing-for-agents` and my own `AGENTS.md` files are written. Scope it to human-facing prose, or
edit the description, before turning it loose on agent docs.

`ship` and `ship-light` are written against my own monorepo — they name that repo's setup commands, preview URL
patterns, Linear project prefixes and in-repo skills. Read them before running them anywhere else, and swap those
for yours. `ship-light` escalates to `ship` on its own the moment the work turns out to need a migration, a new
endpoint, or logic changes across more than a few files.

## Install

```
/plugin marketplace add isakfagerlund/skills
/plugin install isak-skills@isakfagerlund-skills
```

## Install a single skill by hand

```sh
git clone https://github.com/isakfagerlund/skills.git ~/src/skills
ln -s ~/src/skills/skills/ship ~/.claude/skills/ship
```

Symlink into `.claude/skills/` in a project instead of `~/.claude/skills/` to scope a skill to that repo.

## Adding a skill

1. Create `skills/<name>/SKILL.md` with `name` and `description` frontmatter.
2. Add `disable-model-invocation: true` for skills only you should ever fire — anything autonomous and
   side-effecting, like `ship`.
3. Put on-demand reference in `skills/<name>/references/` or a sibling file, and point at it from `SKILL.md`,
   so it loads only when needed.
4. Add a row to the table above.
