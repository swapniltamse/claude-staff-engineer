# claude-staff-engineer

> **A Claude Code skill pack that installs Staff Engineer judgment into your AI agent.**
> Stop vibe coding from destroying your DORA metrics.

---

## The Problem in One Sentence

AI agents are great at generating code. They are terrible at knowing when not to.

Left unconstrained, your agent will:

- Create a new utility when three already exist
- Touch 12 files to fix a 2-line bug
- Ship with no rollback plan and no alert coverage
- Generate a new abstraction for every pattern it sees
- Add a dependency without checking its license or CVE history
- Silently blow past a `// DO NOT CHANGE — caused outage 2023` comment

None of this shows up until your Change Failure Rate climbs, your postmortems multiply, and your senior engineers spend their sprints unwinding what the agent shipped last week.

---

## What This Pack Installs

Eight skills that encode what a Staff Engineer does before writing code.

| Skill | What It Enforces |
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

Claude Code picks up skills from `.claude/skills/` automatically. Done.

---

## The DORA Connection

These four metrics separate elite engineering teams from everyone else. Every skill in this pack maps to at least one of them.

```
Deployment Frequency    ← surgical-strike, no-new-abstraction
Lead Time for Change    ← staff-architect, rfc-gate, breaking-change-detector
Change Failure Rate     ← surgical-strike, migration-sentinel, postmortem-check
Mean Time to Restore    ← observability-gate, ownership-chain, exec-summary
```

The `staff-architect` skill scores every proposed change against all four metrics before implementation begins. You get a GREEN / YELLOW / RED rating before a single line is written.

---

## Skill Highlights

### `staff-architect` — Plan Before You Build

Runs a full codebase discovery pass before any code is written. Outputs a **Strategic Architecture Decision Record** covering what already exists, what the change will cost, and what could go wrong. Ends with a DORA Impact Score.

**Code is blocked until you approve the plan.**

---

### `surgical-strike` — 3 Files. Hard Stop.

The research is clear: change size is the strongest predictor of deployment failures.

If your request requires touching 4+ files, the agent stops, cites the CFR risk, and presents three scoped options. It does not start and ask partway through.

---

### `migration-sentinel` — Databases Are Not Reversible

Before any schema change ships:

- Lock duration estimated at current table size
- Down-migration required or change is blocked
- Dual-write strategy required for destructive operations
- Data integrity verification query included

`DROP TABLE` with no dual-write plan is a hard block.

---

### `postmortem-check` — Read the Warnings Before You Touch Anything

Scans every file being modified for:

- `// INCIDENT:` and `// POSTMORTEM:` annotations
- `// DO NOT CHANGE` and `// WARNING:` comments
- Any comment mentioning outage, deadlock, race condition, or timeout in a warning context

If an annotation directly constrains the change, the agent stops and asks for explicit confirmation before proceeding.

**The worst incidents are the ones that have happened before.**

---

### `no-new-abstraction` — Earn the Abstraction

A new interface, base class, or shared utility requires three existing, real call sites. Not hypothetical ones.

If the rule of three is not met, the agent implements inline and leaves a comment marking the extraction point for when the third real use case arrives.

---

### `observability-gate` — If It Can't Be Observed, It Isn't Done

Every feature ships with:

1. One structured log line at a meaningful event boundary
2. One named metric with labels
3. One alert condition stub (even if not yet wired to paging)

Missing any one of the three: the feature is not complete.

---

### `/exec-summary` — PR Summaries That Executives Actually Use

Slash command. Generates a PR summary with three sections that most PR descriptions skip:

- **Business Value** — what metric moves, for which users, by how much
- **Technical Debt Impact (Honest Assessment)** — required to contain at least one non-positive finding
- **Rollback Plan** — exact steps, time to safe, data risk

Formatted for VPs of Engineering and finance stakeholders, not for the engineer who wrote the code.

---

## Who This Is For

**VPs of Engineering** evaluating AI coding tools for team rollout. If your concern is that AI will help juniors ship faster while making the codebase harder for seniors to maintain, this pack addresses that directly. Every skill encodes a constraint that senior engineers apply automatically.

**Staff and Principal Engineers** who are being asked to review AI-generated code. Use this pack to hold the agent to the same bar you hold your teammates to.

**Engineering Managers** whose DORA metrics have slipped since adopting AI coding assistants. This is a structural fix, not a process mandate.

**Hiring Managers** building teams where AI assistance is standard. The skills here encode the judgment gap between junior and senior engineers. They are a forcing function, not a replacement.

---

## The Hidden Cost of Vibe Coding

Vibe coding ships fast. The cost shows up six months later.

- Utilities duplicated three times because the agent did not search before creating
- Abstractions nobody can modify because they were never designed for change
- Migrations that locked production for 45 minutes because nobody checked the table size
- On-call incidents with no runbook because the feature shipped without an owner
- Incidents re-opened because the agent edited around a `// INCIDENT:` comment it never read

These are not hypothetical failure modes. They are the recurring postmortem themes at every organization that adopted AI coding at speed without installing guardrails.

This pack is the guardrails.

---

## Contributing

Each skill encodes a specific opinion about how elite engineering teams work. If your team has a constraint that belongs here — test coverage floors, API contract enforcement, feature flag requirements — open a PR. The format is in each `SKILL.md` file.

---

## License

MIT
