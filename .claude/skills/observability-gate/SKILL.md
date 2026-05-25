---
name: observability-gate
description: Blocks feature completion until structured logging, a metric, and a named alert condition are present in the shipped code.
when_to_use: Apply automatically before marking any feature, endpoint, job, or service as complete.
---

# Observability Gate Skill

Code that cannot be observed in production is incomplete. It does not matter how well it works in staging. If it fails silently at 3am, the on-call engineer is blind. This skill enforces a minimum observability surface before any feature is considered done.

## The Three Requirements

Every feature, endpoint, background job, or service must ship with all three of the following:

**1. One structured log line**
A log entry at a meaningful event boundary (request received, job started, error encountered, state transition completed). It must be structured (key-value or JSON), not a string concatenation. It must include enough context to correlate with other logs (request ID, user ID, entity ID, or equivalent).

**2. One metric emission**
A counter, gauge, or histogram that tracks the operation. The metric must have a name that matches the feature (not a generic `requests_total` that already exists). It must include at least one dimension or label that distinguishes this operation from others.

**3. One named alert condition**
A written definition of what constitutes "this thing is broken." It does not need to be wired into PagerDuty right now. It must exist as a comment or stub that names: the metric to watch, the threshold, and the time window. Example: `// ALERT: error_rate > 1% over 5 minutes → page on-call`.

## The Gate Check

Before completing a task, scan the code for each requirement and output:

```
## Observability Gate

### Structured Log
[PRESENT — file:line, log level, fields included]
[MISSING — no structured log found for this feature path]

### Metric
[PRESENT — metric name, type, labels]
[MISSING — no metric emission found]

### Alert Condition
[PRESENT — condition defined at file:line]
[MISSING — no alert condition defined]

### Verdict
[GATE PASSED — all three present]
[GATE BLOCKED — missing: list what is missing]
```

If the gate is blocked, add the missing observability before presenting the work as complete.

## What Good Looks Like

```python
# Structured log
logger.info("payment.processed", amount=amount, user_id=user_id, provider=provider, duration_ms=elapsed)

# Metric
metrics.increment("payment.processed.count", tags={"provider": provider, "status": "success"})
metrics.histogram("payment.processed.duration_ms", elapsed, tags={"provider": provider})

# Alert condition stub
# ALERT: payment.processed.count error rate > 0.5% over 10 min → page payments-oncall
```

## Exceptions

Background jobs that run on a schedule and have no user-facing impact may omit the alert condition stub if the job's failure is surfaced by a parent monitor. This exception must be documented inline.

Read-only endpoints (e.g., health checks, status pages) require only the metric. Structured log and alert are optional for these.

All other cases: no exceptions.
