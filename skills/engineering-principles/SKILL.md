---
name: engineering-principles
description: Engineering decision rules for implementation, refactoring, debugging, and verification. Read before writing or changing code, when making design decisions, when investigating a bug, or when choosing how to verify a change.
---

# Engineering principles

Apply the principles whose triggers match the work. They guide decisions within the requested scope; they do not authorize additional work or external actions. Project requirements and the user's choices take precedence. When principles pull in different directions, choose the smallest change that meets the requirement and explain the tradeoff if it matters to the result.

Adapted from the principle skills indexed by [pstack's poteto-mode](https://github.com/cursor/plugins/blob/main/pstack/skills/poteto-mode/SKILL.md). This is a condensed local collection, not a verbatim copy. Numbers are stable so individual principles can be discussed or removed without renumbering the rest.

Before writing or changing code, name the data shape and apply principle 11, Model the domain, when choosing its structure.

In your final reply, name each principle that shaped a decision and the specific choice it changed. Report only principles you read and applied during this session.

## Core

### 1. Laziness protocol

When refactoring, sizing a change, or considering an abstraction, make the smallest change that solves the problem. Consolidate repeated decisions in one place. Prefer deleting complexity to adding layers or boilerplate.

### 3. Redesign from first principles

When a new requirement strains the existing design, ask what you would build if that requirement had existed from the start. Read the affected code and consider the whole design, then deliver the needed change incrementally. Update affected types, callers, documentation, and examples together.

### 5. Subtract before you add

When evolving an area, remove relevant dead code and redundant paths before building on it. Design for observed usage. Avoid speculative validators, options, and abstractions. Keep cleanup tied to the requested change.

### 6. Minimize reader load

When code is hard to trace, examine both the layers a reader must follow and the mutable state they must remember. Collapse pass-through wrappers, derive values instead of synchronizing copies, and keep state local. An abstraction should hide enough complexity to justify learning its interface.

### 7. Outcome-oriented execution

During planned rewrites or migrations, work toward the agreed end state instead of accumulating temporary compatibility code. Temporary breakage must be scoped, reversible, and bounded by explicit verification points. Verify the complete result before declaring the migration done.

### 8. Experience first

When product or design choices compete with implementation convenience, judge them by the experience of the people using and maintaining the result. Justify each control and option. Favor a complete core flow, including feedback and error states, over more unfinished features.

### 10. Build the lever

For nontrivial work, build the smallest rerunnable tool that performs or proves it, such as a codemod, generator, analysis script, or verification check. Try one unit manually to establish the expected result, then compare the tool against it. Make reruns safe. Keep the tool when later work or review depends on it.

## Architecture

### 11. Model the domain

When stateful logic accumulates conditionals or repeated assumptions, encode the domain in a suitable structure. Use a state machine, typed variant, registry, reducer, or collection where it removes invalid states or duplicated rules. Organize modules around the knowledge they own. Keep straightforward local code when an abstraction would only add indirection.

### 12. Boundary discipline

When handling external input or framework integration, parse and validate data where it enters the system. Pass validated domain values into business logic instead of repeatedly checking the same facts. Keep framework and transport details out of that logic, and use pure functions where practical.

### 13. Type system discipline

When designing types or signatures, represent valid states directly. Use variants for mutually exclusive cases and distinct types for values that must not be interchangeable. Parse external data, handle variants exhaustively, and derive types from authoritative schemas. Strengthen types where they prevent a real invalid state; avoid casts that merely hide an unproven assumption.

### 14. Make operations idempotent

When commands or processing steps may be retried, design them to converge on the same result after repeated or interrupted runs. Compare current state with the desired state and apply the difference. Account for partial writes and stale locks. Check both a repeated run and recovery from interruption.

### 15. Migrate callers then delete legacy APIs

When replacing an internal API, inventory its callers, migrate them, and remove the old path in the same coordinated change. Apply this when external users do not require compatibility and the project can absorb the change. If an adapter is necessary, give it a clear removal condition.

## Verification

### 17. Prove it works

Before declaring completion, inspect or exercise the actual result. Run the affected behavior, read the real value, or inspect the resulting artifact. Choose evidence that demonstrates the requested outcome. A successful build or another agent's report proves only what it directly checked.

### 18. Fix root causes

When debugging, reproduce the symptom and trace it to the first incorrect decision or unhandled legitimate state. Instrument when the cause is unclear. If the failing state is valid, handle it; if it should never exist, fix what produced it. Check for other occurrences of the same fault. In investigation-only work, report the cause and proposed correction without implementing it.

### 19. Sequence work into verifiable units

For multi-step changes, choose small units with explicit checks and verify each before building on it. Order delivery so the evidence is understandable, such as a baseline before an optimization or a regression test before its fix. For planned migrations, agree on the verification boundaries before accepting temporary breakage.

### 20. Test behavior, not implementation

When writing or reviewing tests, assert results visible through the interface consumers use. A test should fail if the promised behavior breaks and pass if only the internals change. Derive expected results from the requirement. External effects are behavior; private helper calls usually are not.

### 21. Explain the number

Before reporting or acting on a measurement, establish what it measured and what limits it. Rule out skipped work, errors, caching differences, and noise. Keep the run count, variation, conditions, and evidence with the result. Distinguish a measured bottleneck from a hypothesis about it.

## Delegation and autonomy

### 22. Guard the context window

When investigation produces large outputs, read focused sections and retain findings with source pointers. Bound the scope of each phase. When delegation is available and authorized, give bounded investigations to agents and bring back evidence and concise findings rather than raw output.

### 23. Never block on the human

For reversible work within the authorized task, make reasonable implementation choices and present the result for correction. Ask when a missing product decision actually blocks useful work, and continue independent work while waiting. Respect explicit approval requirements and action permissions.

## Maintaining the workflow

### 24. Encode lessons in structure

When the user identifies a repeated mistake, consider a lint rule, type, metadata field, runtime check, or script that prevents it. Prefer one enforceable rule over repeated instructions. If the lesson requires judgment, keep a clear instruction with an example of the failure it prevents.
