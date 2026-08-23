# skills

[Claude Code](https://claude.com/claude-code) skills I use, packaged as a plugin marketplace.
They also work in Codex and OpenCode, which read the same `SKILL.md` format.

Browse [`skills/`](skills/) to see what's here. Each skill's `SKILL.md` starts with a
`description` line saying what it does and when to fire it, so the directory is the list.

Three things you can't tell from the directory:

- `triage` loads `evidence`. Install them together.
- `writing-for-agents` loads its own [`SKILL-MECHANICS.md`](skills/writing-for-agents/SKILL-MECHANICS.md)
  when the document being written is a skill.
- `ship` and `ship-light` are written against my own monorepo. They name that repo's setup
  commands, preview URL patterns, Linear project prefixes and in-repo skills. Read them before
  running them anywhere else and swap those for yours. `ship-light` escalates to `ship` by itself
  once the work turns out to need a migration, a new endpoint, or logic changes across more than a
  few files.

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

## Adding a skill

1. Create `skills/<name>/SKILL.md` with `name` and `description` frontmatter. Write the
   `description` well. It is the only thing the model sees when deciding whether to load the skill,
   and it's what shows up in this repo's listing.
2. Add `disable-model-invocation: true` for skills only you should ever fire, meaning anything
   autonomous and side-effecting like `ship`.
3. Put on-demand reference material in `skills/<name>/references/` or a sibling file and point at it
   from `SKILL.md`, so it loads only when needed.
