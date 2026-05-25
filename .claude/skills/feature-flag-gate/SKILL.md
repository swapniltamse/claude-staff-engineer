---
name: feature-flag-gate
description: Requires a feature flag for any user-facing change, giving teams an instant kill switch without a deployment.
when_to_use: Apply automatically when a change touches user-facing behavior, a new endpoint, a changed UI path, or any code that affects what a user sees or experiences.
---

# Feature Flag Gate Skill

The fastest rollback is not a git revert. It is a flag toggle. A revert requires a new build, a new deployment, and pipeline time. A flag toggle takes seconds. This skill ensures that user-facing changes ship with that option available.

This directly protects two DORA metrics: Change Failure Rate (you can turn it off before it spreads) and Mean Time to Restore (seconds to dark, not minutes to redeploy).

## What Requires a Feature Flag

A feature flag is required when the change:

- Introduces or modifies behavior a user can see, click, or trigger
- Adds or modifies an API endpoint that external clients call
- Changes a pricing, permission, or access control path
- Affects a background job that processes user data
- Introduces a new integration with a third-party service
- Is being rolled out to a percentage of users rather than all at once

A feature flag is NOT required for:
- Internal refactors with no behavior change
- Dependency upgrades with no API changes
- Infrastructure changes with no user-visible effect
- Bug fixes that restore previous behavior

## Output Required Before Implementation

```
## Feature Flag Gate

**User-facing change detected:** [yes / no]
**Flag required:** [yes / no — reason if no]

### Flag Specification
Flag name:     [service.feature.description — snake_case, namespaced]
Default value: [true / false — and why]
Rollout type:  [boolean / percentage / user-segment]
Kill switch:   [what turning this to false does, in one sentence]

### Rollout Plan
Phase 1: [internal / dogfood — % of traffic or user list]
Phase 2: [gradual rollout — % and criteria to advance]
Phase 3: [full rollout — criteria to remove the flag entirely]

### Flag Cleanup
Estimated removal date: [sprint or date]
Owner responsible for cleanup: [team or role]
```

## What Happens Without a Flag

If a user-facing change ships without a feature flag and something goes wrong in production, the options are:

1. Revert: new commit, new build, new deployment. 15 to 45 minutes depending on pipeline.
2. Hotfix: same timeline, higher stress, same risk of introducing a second bug.
3. Live with it until business hours.

A flag makes option 4 available: turn it off in 30 seconds while the team investigates.

## Flag Debt

Feature flags that are never cleaned up become permanent complexity. Every flag is an if-branch that every future engineer has to reason about. The gate includes a cleanup date for this reason. An uncleaned flag more than two sprints past its estimated removal date is tech debt.

Flag debt is real. This skill tracks the creation. Your team is responsible for the cleanup.
