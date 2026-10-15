# Skill mechanics

The skill-specific branch of [`writing-for-agents`](SKILL.md): what changes when the document is a skill — frontmatter, the invocation choice, and router skills. Everything else about writing it is the universal reference in `SKILL.md`.

## Invocation

Two choices, trading the two loads:

- A **model-invoked** skill keeps a `description`, so the agent can fire it autonomously — and other skills can reach it. You can still type its name: model-invocation always _includes_ user reach; a description only ever adds agent discovery, never removes the human's. The description is the skill's top-level context pointer, forced to stay loaded at all times — permanent context load in exchange for discoverability. A model-invoked skill whose content is all reference is also one home for shared reference: another skill can invoke it, so reference needed by several skills lives in one place. Mechanics: omit `disable-model-invocation`, and write a model-facing description carrying the trigger branches (the pointer-writing rules in `SKILL.md` apply in full).
- A **user-invoked** skill is selected explicitly by the user rather than through automatic discovery. Set `disable-model-invocation: true` and use a short human-facing description. An explicitly invoked router can still read its `SKILL.md` by path and follow it as a workflow; the user does not need to invoke each routed skill separately.

Enable model invocation when the agent should discover the skill on its own. Keep entry commands user-invoked when the user should choose that mode. Explicit file references let workflows share instructions independently of automatic discovery.

Keep shared guidance in one file and link to it from each workflow that needs it. That file can be a shared skill or an ordinary reference document.

## Splitting by invocation

The invocation cut of splitting (the sequence cut lives in `SKILL.md`): split off a model-invoked skill when you have a distinct leading word that should trigger it on its own — a trigger word you actually use in your prompts — or another skill must reach it. You pay context load for the new always-loaded description, so that independent reach has to be worth it.

## Router skills

A **router skill** gives the user one entry point. It identifies intent, reads the selected workflow by an explicit file path, and follows it within the user's authorized scope. Keep shared rules and routing here; keep each workflow's steps in its own file. In this repo, [work-mode](../work-mode/SKILL.md) is the main entry point. Individual workflows remain available for direct use.
