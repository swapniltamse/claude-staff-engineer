---
name: ownership-chain
description: Requires explicit ownership declaration for new code — team, runbook, and on-call rotation — before a feature ships.
when_to_use: Apply automatically when new services, endpoints, jobs, or modules are created.
---

# Ownership Chain Skill

Code without an owner is a liability. When it breaks at 2am, nobody knows who to wake up. When it needs to change, nobody knows who to ask for context. When it needs to be deleted, nobody knows if it is safe to remove.

AI agents create code without owners by default. This skill fixes that.

## What Ownership Requires

Every new service, module, background job, API endpoint group, or significant feature must have three things declared before it ships:

**1. A team owner**
Who is responsible for this code? Not a person's name. A team name that survives personnel changes. If no team owns it, it does not ship until someone claims it.

**2. A runbook**
Where is the document that tells an on-call engineer what to do when this breaks? This can be a stub — a file that exists with headings but not yet filled in — but it must exist and be linked.

**3. An on-call rotation**
Which rotation gets paged when this code fails? If no rotation covers it, that is a gap that must be named and resolved, not silently accepted.

## Output Required

When creating new code that requires ownership, output this block before presenting the implementation:

```
## Ownership Chain Declaration

**Code being created:** [module / service / feature name]
**Team owner:** [team name or "UNOWNED — must be assigned before merge"]
**Runbook location:** [path or URL, or "STUB NEEDED — see below"]
**On-call rotation:** [rotation name or "NOT COVERED — gap identified"]

### If Any Field Is Unowned
This code must not merge until all three fields are filled.
An unowned runbook stub at [path] has been created.
Tag [team or person] to claim ownership before this PR closes.
```

## The Runbook Stub

If no runbook exists, generate a stub at an appropriate location in the repo. Minimum required headings:

```markdown
# Runbook: [feature/service name]

**Owner:** [team]
**On-call rotation:** [rotation]
**Last updated:** [date]

## What This Does
[one paragraph]

## Symptoms of Failure
- [ ] TODO

## Diagnostic Steps
- [ ] TODO

## Escalation Path
- [ ] TODO

## Known Issues
- [ ] TODO
```

A stub is not a complete runbook. It is a promise that one will exist. It is better than silence.

## CODEOWNERS Fragment

For new files or directories, output the CODEOWNERS line that should be added:

```
# Add to .github/CODEOWNERS or equivalent
/path/to/new/code    @org/team-name
```

## Why This Is Enforced at Creation Time

Adding ownership after the fact requires the original author to still be available, willing, and remembered. It rarely happens. The window to declare ownership is when the code is being written. After that, it becomes "legacy code nobody owns," which is the most expensive category of code in any organization.
