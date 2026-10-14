---
name: ship
description: Implement a ticket, test it in the app and browser, and deliver a PR with green CI.
argument-hint: "<ticket-id | ticket-url | pasted ticket text>"
disable-model-invocation: true
---

# Ship

Work on the provided ticket, understand the intended outcome, and clarify any missing points that block implementation.
Implement the feature or bug fix, then run the app using `energy setup` and `energy launch`.
Run the `verify` skill on the affected flow; it tests the change in the browser, fixes what it finds, and hands back the screenshot paths.
Create the PR with the `make-pr` skill, passing it the screenshot paths, wait until CI is green and the PR is ready to merge, then report back with the PR link, screenshots, and what you tested; leave merging to the user.
When reporting back, present the screenshots in the order a user sees them; if the harness can render HTML in the conversation (e.g. T3 Code's `html_render`), show them as one HTML slideshow that steps 1 → 2 → 3 with a short caption per slide, otherwise embed the images directly in that order.
