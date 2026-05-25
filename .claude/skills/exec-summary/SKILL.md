---
name: exec-summary
description: Generates an executive-grade PR summary from the current session's work, formatted for engineering leadership and business stakeholders.
disable-model-invocation: true
---

# Exec Summary Skill (`/exec-summary`)

Review everything done in the current session and produce a PR summary formatted for engineering leadership. This is not a technical changelog. It is a business document that happens to describe technical work.

Write for an audience that includes VPs of Engineering, product owners, and finance stakeholders. They care about outcomes, risk, and reversibility. They do not care about implementation details unless those details create risk.

## Output Format

Produce the summary in this exact structure:

---

## PR Summary

**What shipped:** [One sentence. What does this change do for the user or system? Write in plain language. No jargon.]

---

### Business Value

Describe the concrete, measurable impact this change is expected to deliver. Be specific:
- What metric moves, and in which direction?
- Which user segment or system benefits?
- Is this revenue-generating, cost-reducing, risk-mitigating, or compliance-driven?

If the value is indirect (e.g., reduces future engineering cost), say so and quantify where possible. Do not write vague sentences like "improves developer experience." Say what that means in time saved or incidents prevented.

---

### Technical Debt Impact (Honest Assessment)

This section requires honesty, not optimism. Answer each question plainly:

- **Debt added:** Does this change introduce shortcuts, workarounds, or temporary solutions? If yes, describe them. If no, say so.
- **Debt removed:** Does this change pay down any prior technical debt? Describe it.
- **Architectural risk:** Does this change increase coupling, add a new abstraction that could drift, or create a pattern others might replicate incorrectly?
- **Test coverage delta:** Did coverage go up, go down, or stay flat? A PR that ships without tests is a debt event, not a neutral one.
- **Net assessment:** On balance, is this codebase healthier or less healthy after this change?

Do not skip this section or write "no significant debt introduced" without evidence. That phrase is a red flag to senior engineers.

---

### Rollback Plan

Describe exactly how this change is reversed if it causes a production incident:

- **Immediate rollback:** Can this be reverted with `git revert` or a feature flag toggle within 5 minutes? If yes, state how. If no, explain why not.
- **Data risk:** Does this change write to a database, alter a schema, or modify stored state? If yes, describe what a rollback leaves behind and whether it is safe.
- **Downstream impact:** Does reverting this change break anything else that has already shipped on top of it?
- **Time to safe:** From the moment an incident is declared, how long until the system is fully restored?

If the rollback plan is "it's complicated," that is important information. Say it clearly rather than burying it.

---

## Tone and Style Rules

- Write in plain declarative sentences. No bullet points inside the Business Value narrative.
- Do not use the words "robust", "scalable", "seamless", "leverage", "synergy", or "streamline."
- If you are uncertain about a value estimate or rollback step, say "unclear — needs verification" rather than guessing.
- The Honest Assessment section must contain at least one thing that is not purely positive. If everything was perfect, the section is not being written honestly.
