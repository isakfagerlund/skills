# Change outline

A short structural view of the change for reviewers, adapted from HumanLayer's
[`show-me`](https://github.com/humanlayer/skills/tree/main/plugins/show-me) skill. It shows
the shape of the code before the reviewer opens the diff.

Pick one or two views. Never use all of them. Keep only the calls, files, props, states, and
boundaries a reviewer needs. Keep it to about 25 lines. Put a `# comment` at the end of a line
when a name alone doesn't say what it does.

## Views

Logic or business rules as pseudocode:

```text
on(save)
  if content is unchanged
    return cached result
  write new content
  return fresh result
```

Runtime control flow as a call tree:

```text
submitForm
  createSession
    persistPrompt
    launchAgent
  navigateToSession
```

UI structure as a component tree, with the hooks, state, and package boundaries that matter:

```text
<SessionPage> (apps/web/src/routes/session.tsx)
  useSessionEvents()
  <SessionToolbar>
    <RunSkillButton> (packages/ui)
```

A broad refactor as a shallow file tree:

```text
src/
├── commands/       # parses user actions
├── sessions/       # owns session state
└── transport/      # sends API requests
```

Table, endpoint, or type contracts as the changed fields only:

```text
cooling_designs
  project_id   uuid  unique   # one design per project
POST /projects/:id/cooling-design  → 201 { id }
```

Interaction between parts with Mermaid, `sequenceDiagram` or `flowchart TD`, max ~8 nodes.

## Diff or full shape

Use a `diff` fence when the shape already exists and the point is what changed. Match the diff
to the view:

```diff
 <SessionPage>
   useSessionEvents()
   <SessionToolbar>
+    <RunSkillButton />
   <SessionTimeline>
+    <SkillResultCard />
```

```diff
 on(save)
-  write content
+  if content is unchanged
+    return cached result
+  write new content
+  invalidate cache
```

Show the full shape without diff markers when most of it is new, or when diff markers would hide
ownership or order.
