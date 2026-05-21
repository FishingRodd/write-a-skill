# Data Warehouse Harness Pitfalls

## Pitfall: example becomes universal rule

Bad: copying a partition field name, SQL dialect rule, or company naming convention into a generic skill.

Good: keep it in the example skill or project-specific context.

## Pitfall: raw output floods main context

Bad: returning full DDL dumps, lineage trees, test result tables, and sample rows to the main conversation.

Good: subagent returns a concise summary and references source paths or commands.

## Pitfall: validation depends on memory

Bad: relying on the model to remember every SQL rule after compaction.

Good: encode repeatable checks in deterministic hooks or scripts where practical.
