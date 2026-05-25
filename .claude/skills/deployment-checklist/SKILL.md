---
name: deployment-checklist
description: Generates a deployment-specific checklist before any code ships, covering rollout, monitoring, communication, and rollback. Directly reduces Change Failure Rate and Mean Time to Restore.
when_to_use: Apply automatically before any implementation is marked ready to merge or deploy.
---

# Deployment Checklist Skill

Most production incidents are not caused by bad code. They are caused by good code deployed without a plan. No monitoring set up. No rollback tested. No one told the support team. No feature flag in place. The change itself was fine. The deployment was not.

This skill generates a deployment-specific checklist before code ships. Not a generic checklist. One specific to what this change actually touches.

## The Checklist

Generate this before any work is marked complete:

```
## Deployment Checklist

**Change:** [one-line description]
**Risk level:** [LOW / MEDIUM / HIGH — based on blast radius from staff-architect ADR]

---

### Before merge
- [ ] All tests pass locally and in CI
- [ ] Coverage gate passed (no drops in changed files)
- [ ] Feature flag configured [if applicable — name the flag]
- [ ] Breaking change documented [if applicable — migration path noted]
- [ ] Migration sentinel passed [if applicable — down-migration exists]
- [ ] Second reviewer assigned [if blast radius is MEDIUM or HIGH]

### Before deploy
- [ ] Monitoring dashboard exists or has been updated to include this change
- [ ] Alert condition defined for new code paths
- [ ] Runbook updated or stub created
- [ ] On-call rotation informed of deployment and what to watch for
- [ ] Deployment window confirmed (avoid Fridays, end of business, holidays)

### Rollout plan
- [ ] Deploy to staging / lower environment first
- [ ] Smoke test: [list the 2-3 specific things to verify after deploy]
- [ ] Canary or phased rollout configured [if applicable]
- [ ] Full rollout criteria defined: [what signal says this is safe to expand]

### Rollback plan
- [ ] Rollback command or procedure documented: [exact command or steps]
- [ ] Rollback tested in staging [yes / no — if no, flag it]
- [ ] Rollback time estimated: [< 5 min / 5-15 min / > 15 min — explain if long]
- [ ] Data written during this deployment: [is rollback data-safe or does it leave orphans?]

### Communication
- [ ] Stakeholders informed of deployment time [if user-visible change]
- [ ] Support team briefed on expected behavior change [if user-visible]
- [ ] Status page updated [if change affects uptime or performance]
```

## Risk-Tiered Behavior

**LOW risk** (internal refactor, no behavior change): abbreviated checklist, skip communication and canary sections.

**MEDIUM risk** (new feature behind flag, limited blast radius): full checklist, second reviewer recommended.

**HIGH risk** (schema migration, new integration, authentication change, breaking API change): full checklist required, second reviewer required, deployment window during business hours required, on-call explicitly briefed.

## The Friday Problem

High-risk changes deployed on Fridays after 3pm are a leading cause of weekend incidents. This is not superstition. Engineers leave for the weekend, monitoring gets less attention, and the engineers who know the system best are unavailable.

When a HIGH risk change is ready to deploy on a Friday afternoon, output:

```
Friday deploy warning: this change is rated HIGH risk.
Deploying now means your on-call carries it through the weekend.
Recommended: hold until Monday morning when the full team is available.
If this must deploy today, confirm explicitly.
```

## What a Checklist Does Not Do

It does not guarantee a safe deployment. It ensures the work of thinking through the deployment happened before the deployment, not during an incident at 2am.

The most valuable item on the checklist is the rollback plan. An untested rollback is an assumption. An assumption tested in staging is a plan.
