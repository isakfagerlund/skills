# Retrieve missing ticket context

Choose the smallest read that answers the caller's question:

- **Initial read:** fetch the issue and its comments, following pagination to
  capture decisions and changed requirements. Record related-ticket links; open a
  relation only when it constrains this task. Return relevant exact errors,
  criteria, exclusions, decisions, source IDs, update time, and omissions.
- **Text supplement:** retrieve the named missing field, comment, or relation.
  Reuse text already supplied. If the API returns more, summarize only the delta.
- **Image supplement:** identify the requested image from supplied references or
  the specific issue/comment that contains it. Inspect only images relevant to the
  question. Preserve transcript text supplied by the caller.

For a large payload or images, prefer one fresh OpenCode reader subagent if the
exposed tool supports it and the reader has tracker/image access. Give it the
ticket ID, missing question, known facts, and read-only scope. Return compact
text with source field/comment IDs and unread material. Do not delegate again.
Keep raw images, signed URLs, and full tracker JSON out of the parent handoff.
Use the exposed tool schema; there is no Codex `fork_turns` parameter here.

If an image fetch fails or is partial, retry that image once within the reader.
Refresh its reference only when necessary. Then report the missing evidence.
Do not restart the entire ticket read or fetch unrelated images to fill the gap.

If delegation is unavailable, read the bounded missing material directly. Say
when an image cannot be inspected; never infer its contents from the filename.
Unavailable access is a blocker only when the missing material changes the
implementation decision. Stop and report partial context if a tool or runner
budget prevents completion; do not silently treat a truncated thread as complete.
