---
name: surgical-strike
description: Enforces strict blast-radius limits to protect Change Failure Rate (CFR).
when_to_use: Apply this skill automatically on every code change request.
---

# Surgical Strike Skill

You are operating under a strict blast-radius constraint. Every change you make affects real deployments, real pipelines, and real on-call engineers. Your job is to make the smallest correct change, not the most thorough one.

## The Hard Limit: 3 Files

You may touch at most 3 files per task. This is not a guideline. It is a constraint.

If completing the request as stated requires touching 4 or more files, you must stop immediately. Do not attempt a partial implementation. Do not start coding and then ask partway through.

Instead, output this block:

```
## Blast Radius Exceeded — Human Approval Required

This task as described requires changes to [N] files:
- [file 1] — reason
- [file 2] — reason
- [file 3] — reason
- [file 4+] — reason

This exceeds the 3-file surgical-strike limit.

### Why This Matters (DORA Context)
Change Failure Rate (CFR) is one of the four DORA elite metrics. The research is consistent: change size is the strongest predictor of deployment failures. Large changesets:
- Are harder to review and catch errors in
- Fail more often in production
- Take longer to diagnose and roll back
- Inflate lead time for change (another DORA metric)

### Options for You to Choose From
1. Narrow the scope — define a smaller, shippable slice that stays within 3 files.
2. Break this into a sequence of isolated PRs, each deployable independently.
3. Explicitly approve this full-scope change, accepting the elevated CFR risk.

I will not proceed until you choose one of the above.
```

## Behavior Within the Limit

When the task fits within 3 files:

- Make only the changes required to fulfill the request. No opportunistic refactors.
- Do not reformat code you are not changing.
- Do not add comments, docstrings, or type hints to lines you did not touch.
- Do not "clean up" surrounding code unless the task requires it.
- If you notice a bug or smell in code you are not touching, flag it in a comment at the end of your response. Do not fix it inline.

## Deployment Safety Checklist

Before presenting your final diff, verify:

- [ ] All changed files have corresponding test coverage (existing or new)
- [ ] No new environment variables or secrets are introduced without documentation
- [ ] No database migrations are buried inside application code changes
- [ ] The change can be reverted with a single git revert or feature flag toggle

If any checklist item fails, flag it explicitly before the user merges.
