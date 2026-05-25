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

Skills organized by the DORA metric they protect.

### Deployment Frequency

> How often you ship to production. The goal is multiple times per day. The enemy is large batches, flaky CI, and long-lived branches.

| Skill | What it does |
|---|---|
| `surgical-strike` | Hard 3-file limit per change — forces small, shippable slices |
| `no-new-abstraction` | Rule of Three — blocks premature abstractions that balloon PR size |
| `flaky-test-detector` | Catches flakiness patterns before they make CI untrustworthy |
| `feature-flag-gate` | Dark launch behind a flag so code ships decoupled from release |

### Lead Time for Change

> Time from first commit to production. The enemy is big PRs, unclear reviews, architectural confusion, and coupling that makes every task touch ten files.

| Skill | What it does |
|---|---|
| `staff-architect` | Discovery pass + ADR so reviewers understand the plan immediately |
| `rfc-gate` | Triggers a written proposal before high-complexity work begins |
| `breaking-change-detector` | Catches API breaks early before they stall review with migration debates |
| `change-coupling-detector` | Surfaces files that always change together — hidden tax on every PR |

### Change Failure Rate

> Percentage of deployments that cause a production failure. The enemy is large changesets, no tests, untested rollbacks, and skipped incident warnings.

| Skill | What it does |
|---|---|
| `surgical-strike` | 3-file limit directly reduces CFR — smaller changes fail less |
| `migration-sentinel` | Lock analysis, dual-write gate, down-migration required for schema changes |
| `postmortem-check` | Reads incident annotations before touching files — prevents repeat outages |
| `test-coverage-gate` | Blocks shipping when coverage drops in changed files |
| `dependency-audit` | CVE and license check before any new package enters the codebase |

### Mean Time to Restore

> How long to recover when something breaks. The enemy is no observability, no runbook, no feature flag to turn things off, and no one who knows what the code does.

| Skill | What it does |
|---|---|
| `observability-gate` | Requires a log, a metric, and an alert stub before a feature ships |
| `ownership-chain` | Forces team owner, runbook, and on-call rotation on every new module |
| `feature-flag-gate` | Kill switch available in seconds, no deployment needed |
| `deployment-checklist` | Rollback plan required and tested before merge |
| `/exec-summary` | Rollback plan in every PR summary, written for the person managing the incident |

---

## Install

```bash
cp -r .claude /path/to/your/project/
```

Claude Code picks up skills from `.claude/skills/` automatically.

---

## The DORA score

`staff-architect` scores every proposed change against all four metrics before implementation begins.

```
Deployment Frequency    ← surgical-strike, no-new-abstraction, flaky-test-detector
Lead Time for Change    ← staff-architect, rfc-gate, change-coupling-detector
Change Failure Rate     ← surgical-strike, migration-sentinel, postmortem-check
Mean Time to Restore    ← observability-gate, ownership-chain, deployment-checklist
```

GREEN / YELLOW / RED before a single line is written.

---

## What DORA degradation actually looks like

Most teams do not notice their metrics slipping until it is a problem. Here is what the decline looks like in practice, and which skill addresses each pattern.

---

**"Our PRs keep getting bigger and reviews are taking longer."**

This is Lead Time for Change degrading. Common causes: agents that touch too many files per change, coupling between files that forces co-changes, premature abstractions that require updates everywhere.

Skills: `surgical-strike`, `change-coupling-detector`, `no-new-abstraction`

---

**"CI is a coin flip. We're re-running builds constantly."**

This is Deployment Frequency degrading. Flaky tests erode confidence in the pipeline. When engineers learn that red builds are probably nothing, they stop treating red builds as a signal. That is when real failures start slipping through.

Skill: `flaky-test-detector`

---

**"We had an incident and nobody knew who to call or what the runbook was."**

This is MTTR degrading. New code shipped without an owner, without monitoring, and without a runbook. The feature worked fine until it did not, at which point nobody knew what it did or who to wake up.

Skills: `ownership-chain`, `observability-gate`

---

**"We keep having the same incident. Different PR, same root cause."**

This is CFR degrading from institutional memory loss. Engineers leave. Context goes with them. The `// INCIDENT:` comment in the code is the only thing standing between the next PR and a repeat of that postmortem, and agents ignore those comments by default.

Skill: `postmortem-check`

---

**"A schema migration locked our database for 45 minutes."**

This is CFR degrading from missing process around irreversible operations. Nobody checked the table size. Nobody had a dual-write plan. Nobody tested the rollback.

Skill: `migration-sentinel`

---

**"We shipped a breaking API change and three teams were blocked."**

This is Lead Time and CFR degrading together. A change that breaks consumers creates an unplanned coordination event: the breaking team has to help every affected team migrate before anything can move forward.

Skill: `breaking-change-detector`

---

**"We can't roll back. The only option is forward."**

This is MTTR degrading at the worst possible time. No feature flag. No tested rollback. A change that touched the database. The on-call's options are "fix it fast" or "wait."

Skills: `feature-flag-gate`, `deployment-checklist`, `migration-sentinel`

---

## Skill reference

| Skill | DORA metric | Trigger |
|---|---|---|
| `staff-architect` | Lead Time | Any new feature or component |
| `surgical-strike` | Deployment Frequency, CFR | Every code change |
| `dependency-audit` | CFR | Any new package install |
| `migration-sentinel` | CFR, MTTR | Any schema change |
| `no-new-abstraction` | Deployment Frequency | New interface, base class, shared utility |
| `observability-gate` | MTTR | Before any feature is marked complete |
| `ownership-chain` | MTTR | New service, module, or endpoint |
| `rfc-gate` | Lead Time | High blast-radius changes |
| `postmortem-check` | CFR | Before modifying any existing file |
| `breaking-change-detector` | Lead Time, CFR | Any public API or interface change |
| `feature-flag-gate` | Deployment Frequency, MTTR | Any user-facing change |
| `test-coverage-gate` | CFR | Before any implementation is complete |
| `deployment-checklist` | CFR, MTTR | Before any merge or deploy |
| `flaky-test-detector` | Deployment Frequency | Before creating a PR |
| `change-coupling-detector` | Lead Time | When modifying frequently co-changed files |
| `/exec-summary` | MTTR | Slash command — any time |

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
