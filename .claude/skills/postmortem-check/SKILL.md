---
name: postmortem-check
description: Scans files being modified for incident annotations before touching them, surfacing prior postmortem learnings the model would otherwise ignore.
when_to_use: Apply automatically before modifying any existing file.
---

# Postmortem Check Skill

When engineers survive a production incident, the good ones annotate the code. They leave comments like `// INCIDENT-2024-03: this timeout must stay above 30s or the downstream service drops connections`. These comments are the institutional memory of what went wrong and why certain decisions exist.

AI agents ignore these comments unless explicitly told to look for them. This skill makes looking for them mandatory.

## What to Search For

Before modifying any existing file, scan it for annotations matching these patterns:

- `// INCIDENT:` or `// INCIDENT-`
- `# INCIDENT:` or `# INCIDENT-`
- `// POSTMORTEM:` or `/* POSTMORTEM`
- `# postmortem:` or `# POST-MORTEM`
- `// DO NOT CHANGE` or `// DO NOT REMOVE`
- `// WARNING:` (especially near configuration, timeouts, retry logic, or connection pools)
- `// FRAGILE:` or `// HACK:` or `// DANGER:`
- `// SEE RUNBOOK:` or `// SEE INCIDENT:`
- Any comment containing the words "incident", "outage", "cascading", "deadlock", "race condition", "timeout", "memory leak" in context with a warning tone

## Output Format

If annotations are found, surface them before any code is written:

```
## Postmortem Check — Annotations Found

The following incident or warning annotations were found in files you are about to modify.
Read these before proceeding. They represent real production failures.

---

**File:** [path:line]
**Annotation:** [full comment text]
**Interpretation:** [one sentence explaining what constraint this implies for the current change]

---

[repeat for each annotation found]

### Impact on Current Task
[Does any annotation constrain or change what you were about to do?
If yes, describe how. If no, confirm explicitly that the change does not violate the annotated constraint.]
```

If no annotations are found, output a single line: `Postmortem check: no incident annotations found in affected files.`

## What to Do When an Annotation Constrains Your Change

If an annotation indicates that the change you are about to make was tried before and caused an incident, stop. Do not proceed without explicitly acknowledging the prior incident and explaining why the current change is different or safer.

Output:

```
## STOP — Prior Incident Annotation

A prior incident annotation at [file:line] directly constrains this change.

Annotation: [text]

Before proceeding, I need you to confirm one of the following:
1. This change is different from what caused the incident because: [reason]
2. The conditions that caused the incident no longer apply because: [reason]
3. You accept the risk and want to proceed anyway

I will not modify this code until one of the above is confirmed.
```

## Why This Matters

The worst production incidents are the ones that have happened before. A team that loses an engineer loses the memory of why a particular constant is 30 and not 5. The comment they left is the only thing standing between the next engineer (human or AI) and repeating the incident.

An AI that reads code without reading its warnings is an AI that will reopen closed incidents.
