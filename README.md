# claude-staff-engineer

Stop your AI agent from writing code it should not write.

---

## The problem

AI agents are great at generating code. They are terrible at knowing when not to.

Left unconstrained, your agent will:

- Create a new utility when three already exist
- Touch 12 files to fix a 2-line bug
- Ship with no rollback plan and no alert coverage
- Generate a new abstraction for every pattern it sees
- Add a dependency without checking its license or CVE history
- Silently blow past a `// DO NOT CHANGE — caused outage 2023` comment

None of this shows up until your Change Failure Rate climbs, postmortems multiply, and your senior engineers spend their sprints unwinding what the agent shipped last week.

---

## Skills by what goes wrong

These are the complaints. Each one maps to skills that address the root cause.

---

### "Our deploys keep breaking"

The agent ships code that works locally and fails in production. Changesets are too large to review properly. Tests cover the happy path and nothing else. Incident history sits in comments nobody reads.

| Skill | What it does |
|---|---|
| `surgical-strike` | Hard 3-file limit — smaller changesets fail less, full stop |
| `test-coverage-gate` | Blocks shipping when coverage drops in changed files |
| `postmortem-check` | Reads `// INCIDENT:` annotations before touching files — prevents the same outage twice |
| `migration-sentinel` | Lock analysis, down-migration, and dual-write gate before any schema change ships |
| `dependency-audit` | CVE and license check before any new package enters the codebase |

---

### "We can't roll back. The only option is forward."

No feature flag. No tested rollback procedure. A schema change that left data behind. The on-call's options at 2am are "fix it fast" or "wait for the team."

| Skill | What it does |
|---|---|
| `feature-flag-gate` | Kill switch in seconds — no deployment, no pipeline wait |
| `deployment-checklist` | Rollback plan documented and tested before merge, not invented during an incident |
| `migration-sentinel` | Down-migration required or the schema change is blocked |

---

### "CI is a coin flip. We re-run builds constantly."

Flaky tests have trained the team to ignore red builds. When engineers stop trusting CI, real failures slip through. This is one of the most direct attacks on deployment confidence in existence.

| Skill | What it does |
|---|---|
| `flaky-test-detector` | Catches time-dependent, order-dependent, and network-dependent test patterns before they merge |

---

### "We had an incident and nobody knew who to call."

The feature shipped. It broke. Nobody knew who owned it, where the runbook was, or what the monitoring looked like. The on-call spent 30 minutes figuring out which service was failing before they could start fixing it.

| Skill | What it does |
|---|---|
| `ownership-chain` | Forces team owner, runbook, and on-call rotation on every new module |
| `observability-gate` | Requires a structured log, a named metric, and an alert stub before a feature ships |

---

### "We keep having the same incident."

Different PR, same root cause. Engineers leave. The `// INCIDENT:` comment is the only institutional memory of what went wrong. Agents ignore those comments unless told otherwise.

| Skill | What it does |
|---|---|
| `postmortem-check` | Scans every file being modified for incident annotations before writing a line |

---

### "Every PR is bigger than it needs to be."

The agent touched ten files to fix two lines. Or a new abstraction required updates in six places. Or two files that should be independent keep appearing in every diff together.

| Skill | What it does |
|---|---|
| `surgical-strike` | Stops at 3 files and asks the human to scope the work down |
| `change-coupling-detector` | Surfaces files that always change together — exposes hidden architectural debt |
| `no-new-abstraction` | Rule of Three — 3 real call sites required before any new abstraction is created |

---

### "We keep rebuilding things that already exist."

The agent wrote a new date formatter. The codebase has three. It added a new HTTP client wrapper. There are two already. Every duplicate is future divergence.

| Skill | What it does |
|---|---|
| `staff-architect` | Runs a full discovery pass before any code is written — surfaces what already exists |
| `no-new-abstraction` | Blocks new abstractions without proven reuse |
| `dependency-audit` | Checks if a new package duplicates something already in the project |

---

### "A PR review turned into a design debate."

The PR landed and reviewers immediately disagreed with the approach. Now the engineer has to rewrite half of it. The debate should have happened before implementation, not during review.

| Skill | What it does |
|---|---|
| `staff-architect` | Architecture Decision Record required before code — approach is agreed upfront |
| `rfc-gate` | Triggers a written proposal for high-complexity or cross-team changes |

---

### "We shipped a breaking change and blocked three teams."

An API changed. Consumers were not told. The breaking team had to stop everything and help each affected team migrate before anyone could move forward.

| Skill | What it does |
|---|---|
| `breaking-change-detector` | Catches API and interface breaks, requires a migration path before merge |

---

### Need a PR summary that leadership will actually read?

```
/exec-summary
```

Generates Business Value, an honest Technical Debt Assessment, and a Rollback Plan. Written for VPs and finance stakeholders, not the engineer who wrote the code.

---

## Install

```bash
cp -r .claude /path/to/your/project/
```

Claude Code picks up skills from `.claude/skills/` automatically.

---

## For engineering managers: the DORA connection

If you need a business case for this pack, here is how the skills map to the four DORA metrics that separate elite engineering teams from everyone else.

| Metric | What degrades without guardrails | Skills that protect it |
|---|---|---|
| Deployment Frequency | Large batches, flaky CI, coupled changes | `surgical-strike`, `flaky-test-detector`, `no-new-abstraction`, `feature-flag-gate` |
| Lead Time for Change | Big PRs, surprise design debates, hidden coupling | `staff-architect`, `rfc-gate`, `change-coupling-detector`, `breaking-change-detector` |
| Change Failure Rate | No tests, ignored incident history, risky migrations | `surgical-strike`, `test-coverage-gate`, `postmortem-check`, `migration-sentinel`, `dependency-audit` |
| Mean Time to Restore | No observability, no owner, no kill switch | `observability-gate`, `ownership-chain`, `feature-flag-gate`, `deployment-checklist` |

`staff-architect` scores every proposed change GREEN / YELLOW / RED across all four metrics before a single line is written.

---

## Who this is for

**Engineers** who are tired of spending Fridays cleaning up what the agent shipped on Tuesday.

**Staff and Principal Engineers** who are being asked to review AI-generated code and want to hold the agent to the same bar they hold their teammates.

**Engineering Managers** whose teams started moving faster after AI adoption but shipping less reliably. A process mandate does not fix this. A skill that runs before code is written does.

**VPs of Engineering** who need a business case: see the DORA table above.

---

## The hidden cost of vibe coding

Vibe coding ships fast. The cost shows up six months later.

- Utilities duplicated three times because the agent did not search before creating
- Abstractions nobody can modify because they were never designed for change
- Migrations that locked production for 45 minutes because nobody checked the table size
- On-call incidents with no runbook because the feature shipped without an owner
- Incidents re-opened because the agent edited around a `// INCIDENT:` comment it never read

These are not hypothetical. They are the recurring postmortem themes at organizations that adopted AI coding at speed and dealt with the consequences later.

This pack exists for after you have read one too many of those postmortems.

---

## DORA metric deep dive

For teams tracking engineering performance formally. Each metric has its definition, the symptoms that appear when AI coding degrades it, and the skills that address it.

---

### Deployment Frequency

How often you deploy to production. Elite teams deploy multiple times per day. The enemy is large batches, flaky CI, and long-lived branches.

**Signs it is degrading:**
- PRs sit in review for days because they are too large to merge safely
- Engineers re-run CI on failure because flaky tests have made red builds meaningless
- Features are "done" for weeks but stuck behind a risky release

| Skill | What it enforces |
|---|---|
| `surgical-strike` | Hard 3-file limit per change — forces small, independently shippable slices |
| `no-new-abstraction` | Rule of Three — 3 real call sites required before any new abstraction is created |
| `flaky-test-detector` | Catches flakiness patterns before they make CI a coin flip |
| `feature-flag-gate` | Code ships behind a flag, decoupled from the release decision |

---

### Lead Time for Change

Time from first commit to production. The enemy is big PRs, unclear intent, and coupling that forces every task to touch ten files.

**Signs it is degrading:**
- "Quick" fixes take a week because reviewers need to understand too much context
- Files unrelated to the task keep showing up in the diff
- Breaking API changes surface mid-review and stall the whole PR

| Skill | What it enforces |
|---|---|
| `staff-architect` | Discovery pass + ADR so reviewers understand the plan before reading the code |
| `rfc-gate` | Written proposal required before high-complexity work begins — no surprises in review |
| `breaking-change-detector` | Catches API and interface breaks early, requires a migration path |
| `change-coupling-detector` | Surfaces files that always change together — a hidden tax on every future PR |

---

### Change Failure Rate

Percentage of deployments that cause a production incident. The enemy is large changesets, missing tests, untested rollbacks, and ignored incident history.

**Signs it is degrading:**
- The same incident keeps happening because nobody flagged the `// INCIDENT:` comment
- A schema migration locks the database during business hours
- A dependency added without a CVE check triggers a security review after the fact
- Coverage has quietly dropped 20% over six months, one PR at a time

| Skill | What it enforces |
|---|---|
| `surgical-strike` | Smaller changesets fail less — changeset size and CFR are directly correlated |
| `migration-sentinel` | Lock analysis, down-migration, and dual-write gate before any schema change ships |
| `postmortem-check` | Reads incident annotations before touching files — prevents the same outage twice |
| `test-coverage-gate` | Blocks shipping when coverage drops in the files being changed |
| `dependency-audit` | License, CVE, and maintenance health check before any package is added |

---

### Mean Time to Restore

How long it takes to recover when something breaks. The enemy is no observability, no runbook, no kill switch, and code nobody owns.

**Signs it is degrading:**
- Incidents go dark for 30 minutes while engineers figure out which service is failing
- Nobody knows who owns the broken service or where the runbook is
- Rolling back requires a new deployment rather than flipping a flag
- The on-call engineer is looking at logs with no structured fields and no metrics

| Skill | What it enforces |
|---|---|
| `observability-gate` | Requires a structured log, a named metric, and an alert stub before a feature ships |
| `ownership-chain` | Forces team owner, runbook, and on-call rotation on every new module |
| `feature-flag-gate` | Kill switch in seconds — no deployment, no pipeline wait |
| `deployment-checklist` | Rollback plan documented and tested before merge, not invented during an incident |
| `/exec-summary` | Rollback plan in every PR summary, written for the person managing the incident |

---

## Built by

[Swapnil Tamse](https://www.linkedin.com/in/swapniltamse) — Engineering Leader, AI/AI Security, NYC

---

## Contributing

Each skill encodes a specific opinion about how good engineering teams work. If your team has a constraint that belongs here — test coverage floors, API contract enforcement, feature flag requirements — open a PR. The format is in each `SKILL.md` file.

---

## License

Apache 2.0 — free to use and modify. Copyright notices and attribution must be preserved in derivative works.
