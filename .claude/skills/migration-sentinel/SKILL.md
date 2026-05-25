---
name: migration-sentinel
description: Hard gate for database migrations. Enforces reversibility, lock analysis, and dual-write strategy before any schema change ships.
when_to_use: Apply automatically whenever a database migration, schema change, or data backfill is involved.
---

# Migration Sentinel Skill

Database migrations are the most dangerous class of change in production systems. They are often irreversible, they can lock tables under load, and a failed migration at 2am is a different problem than a failed deployment. This skill enforces the discipline that prevents that call.

## Classify the Migration First

Before anything else, classify the operation:

**Reversible** (low risk): Adding a nullable column, adding an index concurrently, adding a new table.

**Conditional** (medium risk): Adding a NOT NULL column with a default, renaming a column with a compatibility shim, adding a foreign key.

**Irreversible** (high risk): DROP TABLE, DROP COLUMN, removing a NOT NULL constraint after data has been written, backfilling a large table, changing a column type.

Irreversible migrations require the full process below. Reversible migrations still require a down-migration.

## Required Output Before Any Migration Runs

```
## Migration Sentinel Report

**Operation type:** [Reversible / Conditional / Irreversible]
**Table(s) affected:** [list]
**Estimated row count:** [number or "unknown — must verify before running"]

### Lock Analysis
- Will this operation acquire a table lock? [yes / no / depends on engine version]
- Estimated lock duration at current table size: [duration or "unknown"]
- Safe to run during business hours? [yes / no / only with lock timeout set]
- Recommended: [run in maintenance window / run with LOCK_TIMEOUT / safe anytime]

### Down Migration
Does a down migration exist that fully reverses this change?
[yes — show the SQL / no — explain why not and what partial reversal is possible]

### Dual-Write Strategy (Irreversible migrations only)
For destructive or breaking schema changes, describe the dual-write window:
1. Phase 1 (deploy): application writes to both old and new schema
2. Phase 2 (migrate): run migration, verify data integrity
3. Phase 3 (cleanup): remove old schema path from application code
4. Phase 4 (drop): run DROP only after Phase 3 has been in production for [N] days

If no dual-write strategy is proposed, the migration is BLOCKED.

### Data Integrity Check
- What verification query confirms the migration succeeded correctly?
- What is the rollback trigger — at what point is it too late to roll back cleanly?

### Verdict
[SAFE TO RUN / RUN IN MAINTENANCE WINDOW / BLOCKED — REQUIRES DUAL-WRITE PLAN]
```

## Hard Stops

These conditions block the migration entirely until resolved:

- `DROP TABLE` or `DROP COLUMN` with no dual-write plan documented
- Backfill on a table with no estimated row count (you cannot safely backfill a table you have not measured)
- `ALTER TABLE ... NOT NULL` on an existing column with no default and no backfill step
- Any migration where the down-migration is "not possible" without a documented data loss acknowledgment from a human

## What Good Looks Like

A migration that earns approval has:
1. A down-migration that can be run immediately if something goes wrong
2. A lock analysis with a concrete recommendation about timing
3. A data integrity verification query ready to run post-migration
4. A dual-write window for any destructive operation

A migration that gets blocked has: "it's a simple DROP, should be fine."
