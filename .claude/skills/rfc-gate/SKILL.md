---
name: rfc-gate
description: Triggers a mandatory RFC when a change is high-complexity, high-blast-radius, or introduces a new architectural pattern.
when_to_use: Apply automatically when staff-architect scores a change as high blast radius, or when a new architectural pattern is being introduced.
---

# RFC Gate Skill

Some decisions should not be made unilaterally by an AI agent, or by a single engineer in a PR. When a change is large enough to affect how the system works architecturally — not just what it does — it needs a written proposal that other engineers can review before implementation begins.

This skill triggers that gate.

## When an RFC Is Required

An RFC is required when any of the following is true:

- The `staff-architect` ADR scored blast radius as HIGH
- A new architectural pattern is being introduced that others will be expected to follow
- A new service or independently deployable component is being created
- An existing public API contract is being changed in a breaking way
- A cross-team dependency is being introduced or modified
- The implementation will take more than one PR to complete

An RFC is NOT required for bug fixes, performance improvements with no API changes, dependency upgrades, or refactors that preserve all existing interfaces.

## What to Do When the Gate Triggers

Stop implementation. Output this:

```
## RFC Gate Triggered

This change meets the threshold for a written proposal before implementation.

Reason: [which condition above triggered the gate]

I have pre-filled an RFC template below. Review it, complete the open sections,
and share it with the relevant stakeholders before proceeding.

Implementation will resume after the RFC is acknowledged.
```

Then generate the RFC template, pre-filled with everything already known from the current session:

```markdown
# RFC: [title]

**Author:** [name or "TBD"]
**Date:** [today's date]
**Status:** DRAFT
**Stakeholders:** [list teams or people who should review]

---

## Problem

[What problem does this change solve? Why does it need solving now?
What happens if we don't solve it?]

## Current State

[How does the system work today in the relevant area?
What are its limitations?]

## Proposed Approach

[Describe the proposed solution at the design level, not the implementation level.
Include diagrams if the change affects data flow or service boundaries.]

## Alternatives Considered

### Option A: [name]
[Description]
**Pros:** [list]
**Cons:** [list]

### Option B: [name]
[Description]
**Pros:** [list]
**Cons:** [list]

### Option C: Do Nothing
[Why is the status quo not acceptable?]

## Blast Radius

[What systems, teams, or users are affected by this change?
What breaks if this goes wrong?]

## Rollout Plan

[How does this ship safely? Phases, feature flags, canary deployment, migration steps.]

## Success Criteria

[How do we know this worked? What metrics move, and by how much?]

## Open Questions

- [ ] [question 1]
- [ ] [question 2]

## Decision

[Leave blank until RFC is resolved]

**Outcome:** [APPROVED / REJECTED / DEFERRED]
**Notes:** [rationale]
```

## After the RFC Is Acknowledged

When the RFC has been reviewed and a decision reached, implementation may proceed. The RFC does not need to be "approved" by a formal process unless your team has one. It needs to be seen by the people who will be affected.

An RFC that nobody read is not an RFC. It is documentation theater.
