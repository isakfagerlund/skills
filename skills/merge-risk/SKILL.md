---
name: merge-risk
description: Rate how risky it is to merge a PR (low, medium, high) across alignment, impact, verification, rollback cost, and error detection. Use when asked to assess merge risk, and at the end of `make-pr`.
---

# Merge risk

## Inputs

Read the PR, its full diff, and the source issue when the body links one:

```bash
gh pr view --json number,title,body,url,files
gh pr diff
```

Judge the code, not the description. Read the changed files closely enough to name the gaps.

## Categories

Each category has a verdict. The descriptions say what low risk looks like.

- **Alignment (poor ⚠️ / decent 👀 / good ✅):** Code aligns with the description and source issue, when present. Behavior assumptions and drive-bys must be documented.
- **Impact (large ⚠️ / medium 👀 / small ✅):** Scope is contained and away from sensitive code paths.
  - Consider blast radius: every way this PR could reach users or consumers, such as layout shift, breakages for consumers, or mobile responsiveness.
  - Sensitive areas carry outsized risk regardless of diff size: auth, permissions, payments, PII, secrets, data migrations, dependency bumps, and infra or config changes. Flag any that are touched, and call out anything that widens access, weakens validation, or logs sensitive data.
- **Verification (poor ⚠️ / decent 👀 / good ✅):** Behavior is verified, where applicable.
  - Changes to the user experience **need** end-to-end proof as screenshots or recordings. Changes that could meaningfully affect performance **need** profiling. Prefer before-and-after evidence to standalone proof. Missing coverage here is a failure.
  - Edge cases must be tested, or documented when they aren't. When only documentation is present, review the code closely and link the user to it.
- **Rollback cost (high ⚠️ / medium 👀 / low ✅):** The change can be reverted once merged. Two-way doors can be walked back; one-way doors can't. Destructive actions and hard-to-reverse decisions (data migrations, deleted data, published APIs, sent messages) are one-way doors.
- **Error detection (poor ⚠️ / decent 👀 / good ✅ / N/A ✅):** If this breaks in production, someone finds out before users report it. Critical flows are monitored. Look for swallowed errors or fallbacks that hide breakage.

## Score

Score each verdict ⚠️ = 3, 👀 = 2, ✅ = 1, and weight it:

| Category | Weight |
| --- | --- |
| Impact | 2 |
| Verification | 2 |
| Rollback cost | 1.5 |
| Error detection | 1 |
| Alignment | 1 |

Score = Σ(score × weight) / Σ(weights). Below 1.5 is **low**, 1.5 up to 2.25 is **medium**, 2.25 or above is **high**.

Keep the number and the calculation to yourself. The output shows only the level and verdicts.

## Output

```markdown
**Merge risk: <low | medium | high>**

**<category>:** <verdict emoji and word>
- <gap that raises risk, one short line, with a link to the file or line when it helps>

<…every category, in the order above>
```

List only gaps. A category with no gaps gets the verdict line and nothing under it.
