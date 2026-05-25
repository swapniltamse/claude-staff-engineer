# claude-staff-engineer

A Claude Code skill pack for engineering teams that care about DORA metrics, code quality, and the long-term cost of moving fast.

---

## The Problem with Vibe Coding at Scale

AI coding assistants are remarkably good at producing working code. They are remarkably bad at knowing when not to.

Left unconstrained, an AI agent will:

- Create a new utility when three already exist in the codebase
- Touch twelve files to implement a two-line fix
- Ship a PR with no rollback plan and no honest assessment of the debt it creates
- Generate code that works today and costs you a week of cleanup six months from now

This is not a complaint about AI. It is a description of what happens when a powerful code-generation tool operates without the judgment a senior engineer would apply. The tool does not know about your DORA metrics. It does not feel the pain when your Change Failure Rate climbs. It does not sit in the postmortem.

You need to install that judgment as a skill pack.

---

## What This Pack Does

`claude-staff-engineer` gives Claude three behavioral constraints drawn from how Staff and Principal Engineers actually work:

**staff-architect** forces discovery before creation. Before any code is written, Claude searches the workspace for existing utilities, audits the blast radius of the proposed change, and produces a written Architecture Decision Record covering what was found, what it will cost, and what could go wrong. Code generation is blocked until you approve the plan.

**surgical-strike** enforces a hard limit of 3 files per change. This is not a style preference. It is a DORA-aligned constraint. The research on elite engineering teams is consistent: smaller changesets ship faster, fail less often, and recover faster when they do fail. If a request exceeds the limit, Claude stops, explains the deployment risk in terms of Change Failure Rate and Lead Time for Change, and presents scoped options before touching anything.

**exec-summary** produces an executive-grade PR summary on demand via `/exec-summary`. It covers Business Value, an honest Technical Debt assessment (the section most PR descriptions skip), and a concrete Rollback Plan. Formatted for VPs of Engineering and finance stakeholders, not for the engineer who wrote the code.

---

## Why DORA Metrics Are the Right Frame

The DORA research program has tracked engineering team performance across thousands of organizations for over a decade. The four key metrics (Deployment Frequency, Lead Time for Change, Change Failure Rate, and Mean Time to Restore) are the most validated framework for distinguishing elite teams from average ones.

The hidden cost of unconstrained AI coding shows up directly in these numbers:

**Change Failure Rate** increases when changesets are large, discovery is skipped, and rollback plans are absent. An AI that touches ten files to implement a feature is generating CFR risk with every keystroke.

**Lead Time for Change** increases when code review is harder because the diff is sprawling, when test coverage is unclear, and when reviewers cannot quickly understand what changed and why.

**Mean Time to Restore** increases when there is no rollback plan, when changes touch shared infrastructure, and when the on-call engineer cannot determine which of the twelve modified files caused the incident.

**Deployment Frequency** decreases when teams lose confidence in their pipeline because recent AI-assisted changes caused incidents. The team slows down to compensate for a trust deficit the tooling created.

This pack addresses each of these failure modes directly.

---

## The Hidden Cost of Code Bloat

Every net-new file is a file someone will read, maintain, debug, and eventually delete. Every duplicated utility is a divergence point waiting to cause a bug. Every abstraction created for a single call site is complexity the next engineer has to navigate before they can make their change.

Senior engineers develop an instinct for this cost over years of postmortems and refactors. They reach for existing code before writing new code. They ask "is this really a new concept, or is it the same concept wearing different clothes?" They know the codebase well enough to know what already exists.

AI agents do not have this instinct by default. They are trained to complete tasks, not to protect codebases. The `staff-architect` skill installs the discovery discipline that a senior engineer applies automatically.

---

## Who This Is For

**VPs of Engineering** evaluating AI coding tools for team rollout. If your concern is that AI will help junior engineers ship faster while making the codebase harder for senior engineers to maintain, this pack is designed to address that directly.

**Hiring Managers** building teams where AI assistance is the norm, not the exception. The skills in this pack encode the judgment that distinguishes senior engineers from junior ones. They are a forcing function, not a replacement for that judgment.

**Staff and Principal Engineers** who are being asked to review AI-generated code and want to hold the tool to the same standards they hold their teammates to.

**Engineering Managers** whose teams are seeing DORA metrics slip after adopting AI coding assistants and want a structural fix rather than a process mandate.

---

## Install

Copy the `.claude` folder to any project root:

```bash
cp -r .claude /path/to/your/project/
```

That's it. Claude Code picks up skills from `.claude/skills/` automatically. The skills activate based on their `when_to_use` configuration.

To use `/exec-summary`, type it as a slash command in any Claude Code session inside the project.

---

## Skills Overview

| Skill | Trigger | What It Does |
|-------|---------|--------------|
| `staff-architect` | Any new feature, component, or service request | Discovery pass + Architecture Decision Record before any code |
| `surgical-strike` | Every code change | Hard 3-file limit with DORA justification on violation |
| `exec-summary` | `/exec-summary` slash command | Business Value + Honest Debt Assessment + Rollback Plan |

---

## Contributing

The skills in this pack encode specific opinions about how good engineering teams work. If your team has a different constraint that belongs here (test coverage floors, API contract enforcement, migration safety checks), open a PR. The format is documented in each `SKILL.md` file.

---

## License

MIT
