# Data Warehouse Harness Notes

This is an example, not a universal requirement.

## Generic pattern

| Layer | Purpose | Example |
|------|---------|---------|
| Persistent context | Preserve current iteration constraints across compaction | Project-level context file |
| Deterministic validation | Enforce repeatable rules outside model memory | Post-write hook or validation script |
| Subagent isolation | Keep large reads and test outputs out of main context | Schema explorer, quality checker |
| Governance | Bound risk, cost, retries, and trace | Budget and stop conditions |

## Boundary rule

Only the pattern is reusable. Field names, SQL dialect rules, partition rules, and platform commands belong to a specific project or example.
