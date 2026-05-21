---
name: data-warehouse-harness
description: Design a generic data warehouse workflow skill with harness-level context persistence, deterministic validation, and subagent isolation. Use when: building Claude Code skills for data warehouse SQL development, ETL validation, lineage exploration, data quality checks, or other high-context data workflows. Do NOT use for: non-data workflows or copying one company's warehouse rules as universal standards.
---

# Data Warehouse Harness Skill

## Overview

| Mode | Trigger | Entry |
|------|---------|-------|
| **DESIGN** | Create a data workflow skill | → Phase 1 |
| **VALIDATE** | SQL or data workflow needs deterministic checks | → Phase 2 |
| **ISOLATE** | Lineage, schema, test output, or comparison data is too large | → Phase 3 |
| **LEARN** | Absorb domain constraints without polluting global rules | → Phase 4 |

---

## Phase 1: Design Data Workflow Skill

**Goal**: turn a data development process into a governed skill.

**Actions**:
- Identify the workflow stages: requirement analysis, design, SQL/ETL authoring, validation, comparison, performance review, and release checks.
- Separate domain-specific rules from reusable harness patterns.
- Define what belongs in persistent context, hooks, subagents, and examples.

**Completion criteria**:
- Workflow stages have clear entry and completion criteria.
- Domain rules are scoped to the project or example, not promoted to universal rules.
- Harness boundaries are explicit.

---

## Phase 2: Add Deterministic Validation

**Goal**: move repeatable checks out of LLM memory and into deterministic guards.

**Actions**:
- Identify rules that should not rely on conversation memory.
- Route file-write checks through hooks or scripts where the host harness can enforce them.
- Use blocking exits for critical violations when supported by the host harness.

**Completion criteria**:
- Critical rules have deterministic checks.
- Validation output is concise and actionable.
- Non-critical warnings do not derail the primary workflow.

---

## Phase 3: Isolate High-Context Work

**Goal**: prevent large schema, lineage, test, or comparison output from polluting the main context.

**Actions**:
- Assign high-token read-only exploration to a dedicated subagent.
- Assign data quality execution or bulk comparison to a dedicated subagent.
- Require subagents to return summaries, not raw dumps.

**Completion criteria**:
- Main context receives only PASS/FAIL, key findings, and file paths.
- Raw logs, samples, and expanded DDL stay outside the main conversation.
- Subagent responsibilities are narrow and auditable.

---

## Phase 4: Persist Domain Semantics Safely

**Goal**: preserve important domain constraints without turning examples into global rules.

**Actions**:
- Store iteration-specific constraints in project context files.
- Store reusable pitfalls or user preferences in memory only when they apply beyond the current task.
- Keep industry examples in repository-level `examples/` directories.

**Completion criteria**:
- Temporary state, execution logs, and long-term memory are separated.
- Domain examples remain examples.
- Generic skill rules remain domain-neutral.

---

## BLOCK / ASK / LOG Decision Table

| Situation | Decision | Action |
|-----------|----------|--------|
| A domain example is being promoted into a universal rule without evidence | **BLOCK** | Keep it as an example or require explicit generalization evidence |
| Validation would modify or delete production data | **BLOCK** | Require explicit authorization and safer validation path |
| Context-heavy output is needed only for a summary | **LOG** | Delegate to subagent and return structured summary |
| A rule is project-specific but useful later | **LOG** | Store in project context or memory with scope and reason |
| Multiple modeling choices are valid and business semantics differ | **ASK** | Recommend one, explain trade-off, wait for domain decision |

---

## Safety Rails

**NEVER**:
- Never encode one organization's warehouse naming, partition, or SQL rules as universal skill requirements.
- Never paste large raw lineage, schema, or test outputs into the main context when a summary is enough.
- Never rely on LLM memory for critical validation rules that can be checked deterministically.
- Never mix temporary iteration state with long-term memory.

**ALWAYS**:
- Keep generic harness patterns separate from domain examples.
- Use hooks or scripts for deterministic validation when practical.
- Use subagents for high-token exploration and test output.
- Return concise structured summaries from subagents.

---

## Supporting Files

| File | Purpose | Read when |
|------|---------|-----------|
| `harness.md` | Generic harness layering and tool governance | Designing validation, persistence, and subagent boundaries |
| `subagents.md` | Example subagent roles for data workflows | Defining SQL validation, lineage exploration, or data quality agents |
| `pitfalls.md` | Example mistakes when turning domain rules into generic skills | Auditing whether examples polluted generic rules |
