---
name: dependency-audit
description: Audits any new dependency before it enters the codebase, covering license, maintenance health, CVEs, and bundle impact.
when_to_use: Apply automatically whenever a new package, library, or external dependency is being added.
---

# Dependency Audit Skill

Before any `npm install`, `pip install`, `go get`, `cargo add`, or equivalent command executes, you must complete this audit. Do not add the dependency first and audit second. The audit happens before the install.

## What You Must Check

For every proposed new dependency, research and report on all of the following:

**License**
- What is the license? (MIT, Apache-2.0, GPL-3.0, AGPL, proprietary, etc.)
- Is it compatible with this project's license?
- Does it have any copyleft clauses that could affect distribution?
- Flag GPL and AGPL as high-risk for commercial or closed-source projects. Stop and require explicit approval before proceeding.

**Maintenance Health**
- When was the last commit to the main branch?
- Is the project actively maintained or in maintenance-only mode?
- How many open issues vs. closed? Is the ratio worsening?
- Does it have a single maintainer with no succession plan? (bus factor = 1)
- Flag any package with no commits in 12+ months as a maintenance risk.

**Security**
- Are there any known CVEs against this package or its direct dependencies?
- Does the package request unusual permissions or access (filesystem, network, env vars) that the task does not require?
- Flag any known CVE as a blocker. Do not proceed without explicit human approval.

**Size and Bundle Impact**
- What is the install size or bundle size contribution?
- Does this dependency bring in a large transitive dependency tree?
- Is there a lighter-weight alternative already in the project or in the ecosystem?

**Duplication Check**
- Does the project already have a dependency that covers this use case?
- Could the needed functionality be implemented in under 20 lines without a dependency?

## Output Format

```
## Dependency Audit: [package-name@version]

**License:** [license] — [SAFE / REVIEW REQUIRED / BLOCKED]
**Last commit:** [date] — [ACTIVE / MAINTENANCE MODE / ABANDONED]
**CVEs:** [none found / list CVEs] — [CLEAR / BLOCKED]
**Install size:** [size] | Transitive deps: [count]
**Duplication risk:** [existing alternative or "none found"]

### Verdict
[APPROVED / REQUIRES APPROVAL / BLOCKED]

Reason: [one sentence explaining the verdict]

### If Blocked or Requires Approval
[Explain what the human needs to decide and what the alternatives are]
```

## Non-Negotiable Blocks

These conditions are hard stops. You cannot add the dependency without explicit human sign-off:

- Any known CVE, regardless of severity score
- GPL or AGPL license in a commercial or closed-source project
- No commits in 24+ months on an actively-used code path
- Package with fewer than 100 weekly downloads (obscure packages have no community to catch bugs)
