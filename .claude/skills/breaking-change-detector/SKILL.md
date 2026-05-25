---
name: breaking-change-detector
description: Detects breaking changes to public APIs, interfaces, and exported symbols, and blocks shipping without a migration path.
when_to_use: Apply automatically when modifying any public API, exported function, interface, event schema, or shared contract.
---

# Breaking Change Detector Skill

A breaking change is any modification that requires consumers of your code to change their code to keep working. Breaking changes in internal platform code, shared libraries, or public APIs are a leading cause of cross-team incidents and deployment coordination failures.

This skill detects them before they ship.

## What Counts as a Breaking Change

**Always breaking:**
- Removing a function, method, class, or exported symbol
- Removing a required field from a request schema
- Adding a required field to a request schema (consumers that do not send it will fail)
- Changing a return type or response schema in a way that removes fields
- Changing the meaning of an existing field (same name, different semantics)
- Changing error codes, error shapes, or error response formats
- Changing event names or event payload schemas in message queues or event buses

**Breaking depending on consumers:**
- Renaming a function or endpoint without an alias or redirect
- Changing default values of optional parameters
- Narrowing the accepted input range (was: any string, now: max 255 chars)
- Adding a new enum value that consumers must handle exhaustively

**Non-breaking:**
- Adding optional fields to a request schema
- Adding new endpoints or methods
- Adding new optional parameters with sensible defaults
- Widening the accepted input range

## Required Output

When a breaking change is detected:

```
## Breaking Change Detected

**Symbol / endpoint / schema changed:** [name]
**Change type:** [removed / signature changed / schema changed / renamed]
**Classification:** [BREAKING / POTENTIALLY BREAKING]

### What Breaks
[Describe concretely what stops working for consumers of this interface.]

### Known Consumers
[List files, services, or teams that depend on this contract, based on codebase search.]
[If consumers are external (API clients, other services), flag that the blast radius is unknown.]

### Migration Path Required
Choose one of the following before this change merges:

Option A — Versioned migration
  Add a v2 endpoint / v2 interface alongside v1.
  Deprecate v1 with a sunset date.
  Remove v1 only after all known consumers have migrated.

Option B — Compatibility shim
  Keep the old signature as a wrapper that calls the new one.
  Mark it @deprecated with a migration note.
  Schedule removal for [next major version or sprint].

Option C — Coordinated cutover
  All consumers are internal and can be updated in the same PR or deployment window.
  List every consumer that must be updated, and confirm all are included in this change.

Option D — Explicit breaking release
  This is an intentional breaking change in a major version bump.
  Confirm: semver major version has been incremented.
  Confirm: CHANGELOG entry documents the break and the migration path.
  Confirm: consumers have been notified.

### Verdict
[BLOCKED — choose a migration option above before proceeding]
```

## Semver Enforcement

When a breaking change is confirmed and approved:

- Verify the version in `package.json`, `pyproject.toml`, `go.mod`, `Cargo.toml`, or equivalent has been bumped to the next MAJOR version
- If it has not, flag it and refuse to mark the task complete until versioning is updated

## Changelog Entry

For every confirmed breaking change, generate a CHANGELOG entry:

```markdown
## [MAJOR.0.0] - [date]

### Breaking Changes

- **[symbol/endpoint name]**: [what changed and why]
  - **Migration:** [what consumers need to do]
  - **Removed in:** [this version]
  - **Deprecated since:** [prior version, if applicable]
```
