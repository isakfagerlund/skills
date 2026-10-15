---
name: lgtm
description: "Let's Get This Money: ship a ticket end to end and merge the PR yourself, no human in the loop. Experimental."
argument-hint: "<ticket-id | ticket-url | pasted ticket text>"
disable-model-invocation: true
---

# LGTM

Read [ship](../ship/SKILL.md) and follow it end to end for the provided ticket, with one change: you merge, so you are also the last reviewer.
After `verify` reports back, open every screenshot and recording yourself and check them against the ticket and the rest of the app: the flow does what was asked, nothing is cut off, misaligned, or off-centre, spacing and typography match neighbouring screens, and there is no leftover debug or placeholder content; fix anything you find and run `verify` again.
Use [babysit](../babysit/SKILL.md) in fix mode for outstanding CI, conflicts, or review feedback. Before merging, require green checks, `verify` evidence covering the whole flow, and resolved review findings.
When CI is fully green and the PR is mergeable, merge it with `gh pr merge --squash --delete-branch` without asking, then report back with the merged PR link, the screenshots in user order, and what you tested.
