---
name: test-coverage-gate
description: Blocks shipping code where test coverage for changed files drops below threshold, and flags untested paths that directly raise Change Failure Rate.
when_to_use: Apply automatically before marking any implementation complete.
---

# Test Coverage Gate Skill

Untested code is a Change Failure Rate event waiting to happen. This skill enforces a minimum coverage floor on every file being changed and refuses to mark work complete when that floor is not met.

This is not about hitting a coverage number for its own sake. It is about ensuring that the code paths most likely to fail in production have at least one automated check that would have caught the failure before it shipped.

## The Coverage Check

Before presenting work as complete, audit every file being modified or created:

```
## Test Coverage Gate

### Files Changed
| File | Existing coverage | Coverage after this PR | Delta |
|------|-------------------|------------------------|-------|
| [file] | [%] | [%] | [+/- %] |

### Verdict
[GATE PASSED — all changed files at or above threshold]
[GATE BLOCKED — coverage dropped in: list files]
[GATE BLOCKED — new file with no tests]
```

**Default threshold:** 80% line coverage on changed files. If the project has a configured threshold, use that instead.

## What Counts as a Coverage Gap

Coverage gaps that block shipping:

- A new function with no test
- A new error handling branch with no test for the error case
- A changed conditional with no test covering the new branch
- A new API endpoint with no integration or contract test
- A modified data transformation with no test for edge cases (empty input, null, overflow)

Coverage gaps that are flagged but do not block:

- Unchanged files in the same PR that are already below threshold
- UI rendering code where behavior is covered by E2E tests (note this explicitly)
- Glue code that is one line calling a thoroughly tested function

## What to Do When Blocked

When a coverage gap is found, write the missing tests before presenting the implementation as complete. Do not present the implementation and note "tests can be added later." Tests added later are tests that often never get added.

If writing a meaningful test requires infrastructure that does not exist (no test database, no mock service), flag it explicitly:

```
## Coverage Block — Infrastructure Gap

A test for [function/path] requires [what infrastructure].
This infrastructure does not currently exist in the test environment.

Options:
1. Add the infrastructure (preferred — describe what is needed)
2. Accept the coverage gap with explicit acknowledgment and a ticket to add it
3. Restructure the code so the logic can be tested without the infrastructure
```

## The Coverage Decay Problem

Coverage tends to decay over time, not in one large drop but in small increments. A PR that drops coverage by 1% is invisible. Twenty PRs like that produce a codebase at 60% coverage where it used to be at 80%.

AI agents are particularly prone to coverage decay because they are optimizing for completing the task, not for maintaining the test suite. Each change looks fine in isolation. The aggregate is a codebase that nobody trusts to deploy on a Friday.

This gate runs on every PR, not quarterly. That is the point.

## Coverage Is Not Confidence

High coverage does not mean the tests are good. A test that calls a function and asserts it does not throw covers the line. It does not cover the behavior.

This gate checks that coverage exists. Your team is responsible for checking that the tests actually verify the right things. Code review is not replaced by this gate — it is supplemented by it.
