---
name: change-coupling-detector
description: Detects files that always change together, exposing hidden coupling that inflates Lead Time for Change and makes every PR bigger than it needs to be.
when_to_use: Apply automatically when modifying files that appear in multiple prior changesets together.
---

# Change Coupling Detector Skill

When two files always change together, it means one of three things: they are genuinely related (fine), they share an abstraction that should be extracted (fixable), or they are coupled in a way that was never intended (a debt problem). In all three cases, the pattern is worth naming before it gets worse.

Change coupling inflates Lead Time for Change because every task that touches File A automatically also touches File B, making every PR bigger, every review harder, and every rollback more complex. It also raises Change Failure Rate because more files changing per PR means more surface area for something to go wrong.

## What to Check

When modifying a file, search recent git history for files that frequently changed in the same commit or PR. If the same two or three files appear together in 60% or more of recent changes involving the file being modified, that is a coupling signal worth surfacing.

Also flag structural coupling visible without git history:

- A service that imports directly from another service's internal modules (not its public API)
- A configuration file that must be updated manually every time a specific code file changes
- A types or schema file that is modified in nearly every PR across the codebase
- Test files that mock the same internal module in more than five places across the suite (the mock is a seam that is changing more than it should)

## Output Format

```
## Change Coupling Report

### Files being modified in this PR
[list]

### Coupling signals found
| File A | File B | Co-change rate | Signal type |
|--------|--------|---------------|-------------|
| [file] | [file] | [X of last Y changes] | [structural / historical] |

### Analysis
[For each coupled pair: is this intentional? What does it mean architecturally?]

### Verdict
[NO COUPLING DETECTED]
[COUPLING PRESENT — see analysis above]
```

## Coupling Classifications

**Intentional cohesion** — files that should change together because they represent the same concept. Example: a component and its test file. No action required, but worth naming so reviewers understand the PR scope.

**Extractable abstraction** — two files change together because they share logic that is duplicated between them. The fix is extraction. Note the pattern and flag it for the next refactor sprint. Do not extract in this PR unless it falls within the 3-file limit.

**Architectural coupling** — a service imports another service's internals. The files should not be coupled but are. This is a debt item. Flag it, link it to the ADR, and recommend a bounded interface between the two.

**God file coupling** — a single file (often `utils.ts`, `helpers.py`, `common.go`) is touched in nearly every PR. This file is a gravity well pulling unrelated code together. It needs to be broken apart. This is a project-level finding, not a PR-level one — surface it to the team.

## Why This Matters for Lead Time

The DORA research on Lead Time is clear: smaller, more focused changes move through review faster and fail less often. Change coupling is a force that makes PRs bigger than the task requires. Every file that must change together is a tax on every future task that touches either file.

Teams that ignore coupling signals end up with codebases where a one-line fix requires touching six files, which requires a large PR, which requires a longer review, which delays deployment.

The time to break coupling is before it calcifies. This skill surfaces it while the team is already in the code.
