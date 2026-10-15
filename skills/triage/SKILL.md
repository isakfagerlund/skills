---
name: triage
description: Investigate a ticket, bug, or feature request, validate the root issue, and report findings for follow-up work without implementing changes.
argument-hint: "[issue ID, URL, pasted ticket, or error signal]"
---

<user_input>
$ARGUMENTS
</user_input>

Investigate the request above and find the root issue, without changing any code.
Read [engineering-principles](../engineering-principles/SKILL.md) and apply the relevant investigation principles, especially testing assumptions and tracing root causes; keep their application within this skill's investigation scope.
Use every tool and MCP you have a connection to; the executor MCP lists all of them, so check it for sources like Linear, Grafana, PostHog and Supabase.
Validate the issue with real data or a reproduction before you report back, and take any screenshots that help show it.
Report a clear understanding of the problem, issue or feature, with evidence and proposed next steps. Implementation begins only when the user requests it, even if the issue is simple.
