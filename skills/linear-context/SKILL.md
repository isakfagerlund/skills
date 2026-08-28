---
name: linear-context
description: Read a Linear ticket, comments, relations, and images through one isolated sub-agent, then return a compact text handoff so tracker payloads and image tokens stay out of the caller's context. Use when Linear is the configured tracker and work starts from a ticket ID, URL, or pasted ticket whose images matter.
---

# Linear context

Turn a Linear ticket ID or URL into one complete text handoff. Keep all Linear
tool results inside a reader sub-agent.

## Caller boundary

1. Start one fresh native general-purpose sub-agent with no inherited conversation
   history. In Codex, use `fork_turns: "none"`. Pass only the ticket ID or URL and
   any specific questions. Do not fetch the ticket or open its images in the
   caller first.
2. Give the reader the workflow and output contract below. It owns every Linear
   tool call for this ticket.
3. Continue from the reader's final text only. Do not copy raw tool results,
   signed upload URLs, or image data into the caller.

If native delegation or Linear access is unavailable inside the reader, stop and
name the missing capability. Keep the isolation guarantee instead of importing
raw tracker output into the caller.

## Reader workflow

Give the sub-agent this assignment, including the ticket ID or URL and the
caller's specific questions:

```text
Read this Linear ticket and return the compact handoff below. Keep all Linear MCP
results and attachments inside your context.

Work in this order:
1. Fetch the issue with relations, then fetch comment pages until no cursor remains.
2. Resolve the ticket's requested outcome, acceptance criteria, exclusions,
   decisions, and open questions from the text.
3. Collect every image reference from the issue and comments. Record where each
   one appeared.
4. Batch all image references into one image-extraction call. Make this your final
   tool call.
5. Immediately write the handoff. Do not call another tool after images enter
   your context.

Use the available Linear tools even when their MCP prefix differs. If image
extraction is unavailable, say which ticket locations contain unread images
without returning their signed URLs.

Return:
- Ticket: ID, title, status, labels, URL, updated time, and branch name when
  available.
- Problem and outcome: the request, expected versus actual behavior, reproduction
  steps, affected component or platform, and the user-visible result.
- Acceptance criteria: explicit criteria first, then clearly marked inferred
  criteria only when needed.
- Scope: exclusions, constraints, dependencies, and related-ticket implications.
- Evidence: exact errors and stack traces, affected versions or conditions,
  suspected causes, linked code or pull requests, and other implementation-relevant
  facts. Cite the issue field or comment ID for each claim.
- Comments: decisions and technical findings, with author and date when identity or
  order matters.
- Image evidence: for each image, its location, what it shows, exact visible text
  that affects the work, and the point of any annotation or comparison.
- Unknowns: only questions that remain unresolved after reading everything.
- Completeness: what was read, plus anything unavailable or intentionally omitted.

Be compact, but preserve exact error messages, labels, values, and requirements
that implementation or review will depend on. Return no raw JSON, image data, or
signed upload URLs.
```

The read is complete when every issue field, comment, relation, and image has
either contributed to the handoff or been identified as irrelevant.

## Follow-ups

Treat the handoff as the ticket source for later work. For missing non-image
details, start a fresh isolated text-only reader. If missing visual detail blocks
the task, start a fresh isolated visual reader and repeat the one-shot sequence.
Never open the image in the caller or continue the original reader after its final
handoff, since either path defeats the context boundary or replays its image
context.
