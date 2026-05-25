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

## Skills by DORA metric

`staff-architect` scores every proposed change GREEN / YELLOW / RED across all four metrics before a single line is written. The other skills enforce the specific constraints that move those numbers.

---

### Deployment Frequency

How often you deploy to production. Elite teams ship multiple times per day. The enemy is large batches, flaky CI, and long-lived branches.

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
- Files that have nothing to do with the task keep showing up in the diff
- Breaking API changes surface mid-review and stall the whole PR

| Skill | What it enforces |
|---|---|
| `staff-architect` | Discovery pass + ADR so reviewers understand the plan before reading the code |
| `rfc-gate` | Written proposal required before high-complexity work begins — no surprises in review |
| `breaking-change-detector` | Catches API/interface breaks early with a required migration path |
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
| `surgical-strike` | Smaller changesets fail less — CFR and changeset size are directly correlated |
| `migration-sentinel` | Lock analysis, down-migration, and dual-write gate before any schema change ships |
| `postmortem-check` | Reads incident annotations before touching files — prevents the same outage twice |
| `test-coverage-gate` | Blocks shipping when coverage drops in the files being changed |
| `dependency-audit` | License, CVE, and maintenance health check before any package is added |

---

### Mean Time to Restore

How long it takes to recover when something breaks. The enemy is no observability, no runbook, no kill switch, and code that nobody owns.

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
| `/exec-summary` | Rollback plan required in every PR summary, written for the person managing the incident |

---

## Install

```bash
cp -r .claude /path/to/your/project/
```

Claude Code picks up skills from `.claude/skills/` automatically.

---

## Who this is for

If you are a **VP of Engineering** evaluating AI coding tools, the concern is usually the same: juniors ship faster but the codebase gets harder for seniors to navigate. Every skill here encodes a check that senior engineers run automatically. It is a structural fix, not a policy doc.

If you are a **Staff or Principal Engineer** reviewing AI-generated code, use this pack to hold the agent to the same bar you hold your teammates to.

If you are an **Engineering Manager** whose DORA metrics started slipping after AI adoption, this is the answer. A process mandate does not work. A skill that runs before code is written does.

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

## Contributing

Each skill encodes a specific opinion about how good engineering teams work. If your team has a constraint that belongs here — test coverage floors, API contract enforcement, feature flag requirements — open a PR. The format is in each `SKILL.md` file.

---

## License

MIT
