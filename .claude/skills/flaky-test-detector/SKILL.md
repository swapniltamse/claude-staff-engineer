---
name: flaky-test-detector
description: Identifies flaky tests in the affected test suite before shipping, because flaky tests kill Deployment Frequency by making CI untrustworthy.
when_to_use: Apply automatically before marking any implementation complete, and before creating a PR.
---

# Flaky Test Detector Skill

A flaky test is a test that sometimes passes and sometimes fails with no change to the code. One flaky test in a suite is an annoyance. Ten flaky tests is a culture problem. When engineers learn that CI is a coin flip, they start ignoring red builds. When they ignore red builds, real failures ship.

This is one of the most direct attacks on Deployment Frequency in existence. Teams with flaky test suites deploy less often not because they move slowly but because they have lost confidence in their own pipeline.

This skill finds and flags flaky tests before they can spread.

## What to Look For

Before shipping new tests, check for the following patterns that cause flakiness:

**Time-dependent behavior**
- Tests that use `Date.now()`, `time.Now()`, `datetime.now()`, or equivalent without injecting a clock
- Tests that assert on specific timestamps or durations
- Tests that use `sleep()` or explicit waits instead of polling for a condition

**Order-dependent behavior**
- Tests that modify shared state (global variables, singletons, in-memory caches) without cleanup
- Tests that assume a specific execution order
- Test files that lack proper `beforeEach` / `afterEach` / `setUp` / `tearDown` lifecycle management
- Database tests that do not reset state between runs

**Network and I/O dependency**
- Tests that make real HTTP calls without mocking or VCR
- Tests that read from the filesystem without using temp directories
- Tests that depend on external services being available

**Concurrency issues**
- Tests that spawn goroutines, threads, or async tasks without waiting for completion
- Tests that assert on the output of operations that run in parallel
- Race conditions in test setup

**Environment assumptions**
- Tests that depend on environment variables being set without defaulting them
- Tests that assume a specific port is available
- Tests that assume the test runner has write access to specific directories

## Output Format

```
## Flaky Test Report

### New tests added in this PR
| Test | Flakiness risk | Reason |
|------|---------------|--------|
| [test name] | LOW / MEDIUM / HIGH | [specific pattern found] |

### Existing tests in affected files
[Scan for known flakiness patterns in tests near the changed code]
| Test | Flakiness risk | Reason |
|------|---------------|--------|

### Verdict
[CLEAN — no flakiness patterns detected]
[REVIEW NEEDED — [N] tests have medium or high flakiness risk]
```

## When Flakiness Is Found

Do not ship a test with HIGH flakiness risk. Fix it before the PR merges.

For MEDIUM risk tests, output a specific recommendation:

```
Flakiness risk: [test name]
Pattern: [what was found]
Fix: [concrete suggestion — inject a clock, use temp directory, add teardown, etc.]
```

## The Cost of One Flaky Test

A single flaky test that fails 10% of the time means one in ten CI runs fails for no reason. If your team runs CI 50 times a day, that is five false failures per day. Each one requires someone to re-run the build, notice it passed the second time, and merge. Small cost per incident, significant cost across a quarter.

If the test is in a critical path and the team learns to re-run on failure, they will eventually stop noticing when the failure is real. That is when a real bug ships.

## Detection vs. Prevention

This skill detects flakiness patterns in code being written now. It does not fix flaky tests already in the suite. If you have a known flaky test problem, the fix is a dedicated cleanup sprint, not a skill.

What this skill does is prevent the suite from getting worse with every PR.
