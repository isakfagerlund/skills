# skills

[Claude Code](https://claude.com/claude-code) skills I use, packaged as a plugin marketplace.
They also work in Codex and OpenCode, which read the same `SKILL.md` format.

## Work mode

**Start with `/work-mode`.** It is the main entry point and chooses the workflow for your request:

```text
/work-mode Why do users lose their saved filters?
/work-mode Fix the saved-filter bug.
/work-mode Implement this ticket and merge it when verified.
```

[work-mode](skills/work-mode/SKILL.md) reads the shared engineering principles and writing rules, then routes to the relevant skills in this repo. It stays active for follow-up work in the session. Investigation remains read-only, and merging requires explicit authorization. Requests for local edits stay local.

Install the full plugin, or the sibling skills referenced by `work-mode`, so its relative links resolve. You can still invoke individual workflows directly.

Browse [`skills/`](skills/) to see what's here. Each skill's `SKILL.md` starts with a
`description` line saying what it does and when to fire it, so the directory is the list.

Two things you can't tell from the directory:

- `writing-for-agents` loads its own [`SKILL-MECHANICS.md`](skills/writing-for-agents/SKILL-MECHANICS.md)
  when the document being written is a skill.
- `ship` is written against my own monorepo. It names that repo's setup commands
  (`energy setup`, `energy launch`). Read it before running it anywhere else and swap those for yours.

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

Symlink into a project's `.claude/skills/` instead of `~/.claude/skills/` to scope a skill to that
repo. Codex reads `~/.codex/skills/`, OpenCode reads `~/.config/opencode/skills/`, and both follow
symlinks, so one clone can serve all three.

## Standing writing rule

Work-mode loads the writing rules for its session. To require them outside work-mode too, add this instruction to your agent's personal or project instructions, such as `AGENTS.md` or `CLAUDE.md`:

> Apply the `unslop` skill to all prose, including user replies. When writing skills or other agent instructions, also apply `writing-for-agents`.

Install both skills to use this rule. The `unslop` description also requests automatic selection for all prose, but selection depends on the host. Installing this plugin does not install a personal or project instruction file.

## Adding a skill

1. Create `skills/<name>/SKILL.md` with `name` and `description` frontmatter. Write the
   `description` well. It is the only thing the model sees when deciding whether to load the skill,
   and it's what shows up in this repo's listing.
2. Add `disable-model-invocation: true` for skills only you should ever fire, meaning anything
   autonomous and side-effecting like `ship`.
3. Put on-demand reference material in `skills/<name>/references/` or a sibling file and point at it
   from `SKILL.md`, so it loads only when needed.
