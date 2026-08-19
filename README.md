# skills

Collection of [Claude Code](https://claude.com/claude-code) skills I use, packaged as a plugin marketplace.

## Skills

| Skill | Invocation | What it does |
| --- | --- | --- |
| [`triage`](skills/triage/SKILL.md) | `/triage [ticket, URL, or error signal]` or model-invoked | Understands a work item, challenges it with production data, and returns findings, options with tradeoffs, and a plan brief. Analysis only — never implements. |
| [`ship-it`](skills/ship-it/SKILL.md) | `/ship-it [ticket id or context]` | Takes finished work from the working tree to an open PR: scoped checks green, conventional commit, branch, push, PR body filled from the project's own template. |
| [`bro`](skills/bro/SKILL.md) | `/bro` or model-invoked | Re-explains the previous assistant message in plain language, for when the reply didn't land. Re-expresses only — never re-answers, never adds information, and every path, command and number survives verbatim. |
| [`evidence`](skills/evidence/SKILL.md) | model-invoked (used by `triage`) | Challenges a load-bearing claim with production data: falsifiable predictions, an evidence ledger, the kill query, and a no-fabrication guard. |

`triage` loads `evidence`, so keep them installed together.

## Install

```
/plugin marketplace add isakfagerlund/skills
/plugin install isak-skills@isakfagerlund-skills
```

## Install a single skill by hand

```sh
git clone https://github.com/isakfagerlund/skills.git ~/src/skills
ln -s ~/src/skills/skills/ship-it ~/.claude/skills/ship-it
```

Symlink into `.claude/skills/` in a project instead of `~/.claude/skills/` to scope a skill to that repo.

## Adding a skill

1. Create `skills/<name>/SKILL.md` with `name` and `description` frontmatter.
2. Add `disable-model-invocation: true` for skills only you should ever fire — anything side-effecting, like `ship-it`.
3. Put on-demand reference in `skills/<name>/references/` and point at it from `SKILL.md`, so it loads only when needed.
4. Add a row to the table above.
