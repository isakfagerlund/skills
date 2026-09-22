# Evidence that changes a decision

Before querying, state the claim, a falsifiable prediction, and how confirmation
or refutation would change the recommendation. Skip queries without that purpose.
For an impact-driven report, first ask whether the reported condition still exists.
Inspect enough code or schema to identify the correct signal before querying it.

Use repository-owned source configuration and runbooks to identify environments,
tables, tenant scope, and read-only tools. Never guess a production project ID,
column, event name, or user/company identifier. Use read-only credentials and
bounded queries. Tracker writes, issue resolution, data changes, and automated
root-cause services are outside this investigation.

Default budget: three telemetry data queries in total, including retries and
delegated queries. Necessary tool discovery/schema reads also count toward the
runner's overall budget. Stop a failed query after one retry; mark what remains
unknown. A runner-supplied lower budget takes precedence. Three inconclusive
queries mean unresolved evidence, not proof that the claim is unanswerable.

For each queried claim, add one record to the handoff's `evidence` array:

- Claim and prediction written before the query.
- Verdict: `confirmed`, `refuted`, or `unknown`.
- Observed result, units, environment, time window, and relevant population filter.
- Source link or tool/result identifier, reproducible query, and retrieval time.
- Coverage limitations and the resulting change to scope or recommendation.

Zero events means no observed events in that source and window. It rules out an
impact claim only if the affected population, instrumentation, retention, and
query coverage are adequate. It never disproves an independently demonstrated
code defect. An error count is not an affected-user count. A failed query is not
zero. Never fabricate numbers or links; use a source identifier when no deep link
exists.

Accept a reader's bounded result excerpt with query, environment, window, and
source provenance. Requery only if that evidence is stale, ambiguous, inconsistent,
or insufficient for the decision. A prose assertion without provenance is still
an unverified claim. Code proof can confirm a defect without production telemetry;
observational counts alone do not establish its causal mechanism.
