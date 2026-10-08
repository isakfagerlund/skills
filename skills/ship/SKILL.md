---
name: ship
description: Implement a ticket, test it in the app and browser, and deliver a PR with green CI.
argument-hint: "<ticket-id | ticket-url | pasted ticket text>"
disable-model-invocation: true
---

# Ship

Work on the provided ticket, understand the intended outcome, and clarify any missing points that block implementation.
Implement the feature or bug fix, then run the app using `energy setup` and `energy launch`.
Test the affected flow in the browser, fix any issues, and take screenshots showing the flow and changes once everything looks good.
Create a PR with the screenshots attached using `gh pr create --attach <screenshot>` for each image, wait until CI is green and the PR is ready to merge, then report back with the PR link, screenshots, and what you tested; leave merging to the user.
