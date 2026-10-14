---
name: lgtm
description: "Let's Get This Money: ship a ticket end to end and merge the PR yourself, no human in the loop. Experimental."
argument-hint: "<ticket-id | ticket-url | pasted ticket text>"
disable-model-invocation: true
---

# LGTM

Read [ship](../ship/SKILL.md) and follow it end to end for the provided ticket, with one change: you merge, so you are also the last reviewer.
After `verify` reports back, open every screenshot and recording yourself and check them against the ticket and the rest of the app: the flow does what was asked, nothing is cut off, misaligned, or off-centre, spacing and typography match neighbouring screens, and there is no leftover debug or placeholder content; fix anything you find and run `verify` again.
Once the PR is open, babysit it (in T3 Code, `watch_pull_request`) until every check is green, the `verify` evidence covers the whole flow, and review comments, if any, are addressed; fix and push whatever fails, and wait again.
When CI is fully green and the PR is mergeable, merge it with `gh pr merge --squash --delete-branch` without asking, then report back with the merged PR link, the screenshots in user order, and what you tested.
