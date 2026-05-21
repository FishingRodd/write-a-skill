# Data Workflow Subagent Examples

These are example roles, not required names.

## sql-validator

Purpose: validate SQL structure and project rules.

Output:
- status: PASS / FAIL
- critical issues
- warnings
- file and line references where possible

## lineage-explorer

Purpose: inspect schema, DDL, and upstream/downstream lineage without flooding main context.

Output:
- table grain
- key fields
- partition strategy
- upstream/downstream summary
- special semantics or risks

## data-quality-checker

Purpose: run or summarize data quality checks.

Output:
- PASS / FAIL summary
- failed checks only
- no raw result dumps unless explicitly requested
