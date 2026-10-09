---
name: triage
description: Investigate a ticket, bug, or feature request, find and validate the root issue, and report a clear understanding of it so the work can continue in another thread (no implementation)
argument-hint: "[issue ID, URL, pasted ticket, or error signal]"
---

<user_input>
$ARGUMENTS
</user_input>

Investigate the request above and find the root issue, without changing any code.
Use every tool and MCP you have a connection to; the executor MCP lists all of them, so check it for sources like Linear, Grafana, PostHog and Supabase.
Validate the issue with real data or a reproduction before you report back, and take any screenshots that help show it.
Report a clear understanding of the problem, issue or feature, so the work can continue in a different thread.
If you judge the problem very simple, don't stop at the report: start a new thread (in T3 Code, `t3_thread_launch` with its own worktree) whose message is `/ship` followed by your full report and the original request.
