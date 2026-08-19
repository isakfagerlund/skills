# Investigation sources — connection details

Single source of truth for how to reach each evidence source. `SKILL.md` and both
sweep role files (`agent-evidence.md`, `agent-analyst.md`) read from here; change
a project id or a query shape once, in this file.

Everything here is **read-only**. During an investigation you query; you never
create, resolve, assign, or mutate.

## Database — Supabase MCP

Production is `gzqknrlohsusnhxcgucx`, staging is `uaiysjhwnlxqaptgnxgg`.
`SELECT`s only, always with a `LIMIT` (20 unless the question needs more).

## Logging — the `gcx` CLI

Loki is `-d grafanacloud-logs`. Stream labels are `service_name` (`api-v2`, `api`,
`autarc-pro-production`, …) and `deployment_environment` (`production`,
`staging`). Everything else (`company_id`, `user_id`, `request_id`,
`http_status_code`, `path`) is structured metadata, filtered after the selector:

```bash
gcx logs query -d grafanacloud-logs \
  '{service_name="api-v2", deployment_environment="production"} | company_id="<uuid>"' \
  --since 24h --limit 100 -o raw
```

Narrow `--since` to the reported window before widening it, and keep `--limit` at
or below 100.

## Exceptions — Sentry MCP

Org `autarc-gmbh`. Go here first whenever the symptom could be a thrown error
rather than bad data — a blank screen, a failed save, "it just doesn't work".
Projects: `autarc-web-production`, `autarc-pro-ios` (Capacitor/native),
`node-nestjs` (legacy API), `autarc-check-*`, `autarc-customer-portal-*`.

- `search_issues` — grouped issues, e.g. `is:unresolved firstSeen:-24h
  level:error` scoped with `projectSlugOrId`.
- `search_events` — individual events with timestamps, and counts.
- `get_sentry_resource` — one issue in full: stack trace, breadcrumbs, release,
  tags.
- `analyze_issue_with_seer` — root cause when the stack trace isn't enough.
  Expensive; only for the candidate root cause, never for triage.

Tie the issue to the report before believing it: matching user/company tag,
release, and a `lastSeen` that overlaps the reported time. An issue that exists
but doesn't overlap is not the bug — say so. Don't `update_issue` (resolve,
assign) during an investigation unless asked.

## Product analytics / session replay — PostHog MCP

`posthog:exec`, project `autarc (production)` (id 26634). Use it for what the
user actually did, which logs and rows can't show. Sentry owns exceptions; come
here for behaviour and replay:

- `query-session-recordings-list` — find the replay for the affected user around
  the reported time.
- `query-trends` — did the behaviour change, and when.
- `execute-sql` (HogQL) — only when no `query-*` tool fits.

The MCP is a CLI behind one tool: `search <name>` → `info <tool>` → `schema
<tool> <field>` (required for any field with a `hint`) → `call`. Confirm event and
property names exist with `read-data-schema` before querying — never take an
event name from the ticket text.

Read-only: query, don't create insights, dashboards, cohorts, or flags.
