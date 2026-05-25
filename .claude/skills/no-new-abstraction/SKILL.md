---
name: no-new-abstraction
description: Enforces the Rule of Three — a new abstraction requires three existing, real call sites before it is created.
when_to_use: Apply automatically whenever new interfaces, base classes, shared utilities, hooks, composables, or generic wrappers are proposed.
---

# No New Abstraction Skill

Premature abstraction is the most common form of technical debt in AI-generated code. A model that sees two similar things immediately wants to extract a shared interface. That instinct is wrong more often than it is right, and the cost compounds every time a future engineer has to understand the abstraction before they can make their change.

This skill enforces a single rule: **a new abstraction requires three real, existing call sites.**

## The Rule of Three

Before creating any of the following, you must demonstrate three existing call sites that would concretely benefit from the abstraction:

- A new interface or abstract class
- A shared utility or helper function
- A generic wrapper, HOC, or composable
- A base class meant to be extended
- A configuration object meant to be reused across features
- Any function whose primary argument is a callback or strategy

"Call sites" means code that exists right now in the codebase. Not code you are about to write. Not hypothetical future use cases. Not "the next engineer will probably need this."

## The Check

When a new abstraction is proposed, run this check and output the result:

```
## Abstraction Gate: [proposed abstraction name]

### Existing Call Sites Found
1. [file:line] — [one sentence describing how this site would use the abstraction]
2. [file:line] — [one sentence describing how this site would use the abstraction]
3. [file:line] — [one sentence describing how this site would use the abstraction]

### Verdict
[APPROVED — 3 real call sites confirmed]
[BLOCKED — only N call sites found]
```

If fewer than three call sites exist, output this instead:

```
## Abstraction Gate: BLOCKED

Only [N] real call site(s) found for [proposed abstraction].

The Rule of Three is not met. Proceed with inline implementation.

When a third real call site emerges naturally, revisit the extraction.
The right time to abstract is after the third duplication, not before the second.
```

## What to Do When Blocked

When the rule blocks an abstraction, implement the logic inline at the call site. Do not create a "temporary" version of the abstraction to work around the gate. Temporary abstractions become permanent ones.

Leave a comment at the inline implementation:

```
// NOTE: inline by design — extract when 3 real call sites exist (Rule of Three)
```

## The Exception

One valid exception: abstractions required by the framework or language idiom (e.g., React context for global state, an interface required to satisfy a third-party type contract). These must be documented as framework-required, not design-driven. They still require a written justification in the ADR.

## Why This Matters

Every abstraction is a contract. Contracts constrain the future. A well-timed abstraction (three proven use cases) is knowledge crystallized into code. A premature abstraction (one use case with guesses about the future) is speculation crystallized into a maintenance burden.

An AI that abstracts eagerly is building a maze for the next human engineer.
