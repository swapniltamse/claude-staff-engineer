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

## What this pack installs

Skills that encode what a Staff Engineer does before writing code.

| Skill | What it enforces |
|---|---|
| `staff-architect` | Discovery pass + Architecture Decision Record before any code |
| `surgical-strike` | Hard 3-file blast-radius limit, DORA-justified |
| `dependency-audit` | License, CVE, maintenance health check before any install |
| `migration-sentinel` | Lock analysis, down-migration, dual-write gate for schema changes |
| `no-new-abstraction` | Rule of Three — 3 real call sites before any new abstraction |
| `observability-gate` | Blocks completion without a log, a metric, and an alert stub |
| `ownership-chain` | Forces team owner, runbook, and on-call rotation on new code |
| `rfc-gate` | Triggers a written proposal for high-blast-radius changes |
| `postmortem-check` | Scans for incident annotations before touching any file |
| `breaking-change-detector` | Catches API/interface breaks and requires a migration path |
| `/exec-summary` | Slash command: executive PR summary with honest debt assessment |

---

## Install

```bash
cp -r .claude /path/to/your/project/
```

Claude Code picks up skills from `.claude/skills/` automatically.

---

## The DORA connection

Four metrics tell you whether your deployment pipeline is working or not. Every skill in this pack maps to at least one.

```
Deployment Frequency    ← surgical-strike, no-new-abstraction
Lead Time for Change    ← staff-architect, rfc-gate, breaking-change-detector
Change Failure Rate     ← surgical-strike, migration-sentinel, postmortem-check
Mean Time to Restore    ← observability-gate, ownership-chain, exec-summary
```

`staff-architect` scores every proposed change against all four before implementation begins. GREEN / YELLOW / RED before a single line is written.

---

## Skill highlights

### `staff-architect`: plan before you build

Runs a full codebase discovery pass before any code is written, outputs a Strategic Architecture Decision Record covering what already exists, what the change will cost, and what could go wrong, and blocks code generation until you approve the plan. Ends with a DORA Impact Score.

---

### `surgical-strike`: 3 files, then it stops

Change size is the strongest predictor of deployment failures. If your request requires touching 4+ files, the agent stops, explains the CFR risk, and presents scoped options. It does not start and ask partway through.

---

### `migration-sentinel`: databases are not reversible

Before any schema change ships:

- Lock duration estimated at current table size
- Down-migration required or change is blocked
- Dual-write strategy required for destructive operations
- Data integrity verification query included

`DROP TABLE` with no dual-write plan is a hard block.

---

### `postmortem-check`: read the warnings before you touch anything

Scans every file being modified for:

- `// INCIDENT:` and `// POSTMORTEM:` annotations
- `// DO NOT CHANGE` and `// WARNING:` comments
- Any comment mentioning outage, deadlock, race condition, or timeout in a warning context

If an annotation directly constrains the change, the agent stops and asks for confirmation before proceeding.

The worst incidents are the ones that have already happened once.

---

### `no-new-abstraction`: earn it first

A new interface, base class, or shared utility requires three existing, real call sites. Not hypothetical ones.

If the rule of three is not met, the agent implements inline and leaves a comment marking the extraction point for when the third real use case arrives.

---

### `observability-gate`: if it can't be observed, it isn't done

Every feature ships with:

1. One structured log line at a meaningful event boundary
2. One named metric with labels
3. One alert condition stub (even if not yet wired to paging)

Missing any one of the three, the feature is not complete.

---

### `/exec-summary`: PR summaries worth reading

Slash command. Generates a summary with three sections that most PR descriptions skip:

- Business Value — what metric moves, for which users, by how much
- Technical Debt Impact (Honest Assessment) — required to contain at least one non-positive finding
- Rollback Plan — exact steps, time to safe, data risk

Written for VPs of Engineering and finance stakeholders, not the engineer who wrote the code.

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
