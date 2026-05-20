# write-a-skill

> A production-grade framework for designing, auditing, learning, and evolving Claude Code skills.

`write-a-skill` helps you turn a rough automation idea into a **well-scoped, auditable, testable, and evolvable Claude Code skill**.

Most skill generators stop at writing `SKILL.md`.

`write-a-skill` goes further:

- **NEW** — design a new skill from a business or engineering workflow
- **AUDIT** — review and improve existing skills with a structured checklist
- **LEARN** — absorb patterns from articles, agent sessions, and other skills
- **EVOLVE** — improve skills with GT datasets, evals, traces, gates, and rollback

If you are building Claude Code skills for DevOps, internal tools, customer support, data workflows, security operations, deployment automation, or domain-specific business processes, this project gives you a reusable engineering framework instead of another prompt template.

[中文说明](README.zh-CN.md)

---

## Why this exists

Claude Code skills are easy to start and hard to make reliable.

A useful skill is not just a prompt. It needs:

- clear trigger boundaries
- safety rails
- phase-based workflows
- decision tables
- progressive disclosure
- traceable tool usage
- evaluation cases
- regression protection
- a way to learn from failures

`write-a-skill` treats a skill as an engineering artifact:

```text
prompt-like document → governed workflow → auditable harness → evolvable system
```

---

## Key ideas

### 1. Design skills with explicit operating modes

A good skill should know whether it is:

- creating something new
- auditing existing behavior
- learning from external examples
- evolving against a test set

### 2. Use progressive disclosure

Keep `SKILL.md` focused. Move deep details into support files:

- `audit-checklist.md`
- `patterns.md`
- `harness.md`
- `evolve.md`
- `pitfalls.md`
- `templates/`

### 3. Treat high-risk workflows as harnesses

For multi-agent, MCP, deployment, remote operation, database, or file-writing skills:

- define tool boundaries
- record traces
- set stop conditions
- classify BLOCK / ASK / LOG decisions
- verify the trajectory, not only the final answer

### 4. Make skills evolvable

Advanced skills can be improved with:

- Ground Truth cases
- dev / holdout / regression split
- per-case trace
- three-layer evals
- five-dimensional AND gates
- checkpoint / rollback

---

## Repository structure

```text
write-a-skill/
├── README.md
├── README.zh-CN.md
├── LICENSE
├── skills/
│   └── write-a-skill/
│       ├── SKILL.md
│       ├── audit-checklist.md
│       ├── patterns.md
│       ├── harness.md
│       ├── evolve.md
│       ├── pitfalls.md
│       └── templates/
│           ├── simple-skill-template.md
│           └── complex-skill-template.md
└── examples/
    └── it-deploy-troubleshooting-skill/
        ├── SKILL.md
        ├── runbook.md
        ├── monitor-agent.md
        ├── pitfalls.md
        ├── harness.md
        └── eval/
            ├── gt.dev.jsonl
            ├── gt.holdout.jsonl
            └── gt.regression.jsonl
```

---

## Installation

Copy the skill into your Claude Code skills directory:

```bash
cp -r skills/write-a-skill ~/.claude/skills/
```

Then use it in Claude Code:

```text
/write-a-skill Create a skill for triaging customer support tickets.
/write-a-skill Audit my deployment skill.
/write-a-skill Learn from this agent session and improve the skill design.
/write-a-skill Evolve this skill using these GT cases.
```

---

## Quick start

### Create a simple skill

```text
/write-a-skill Build a skill that summarizes incident reports into root cause, impact, mitigation, and follow-up actions.
```

For simple workflows, `write-a-skill` uses a lightweight template.

### Create a complex skill

```text
/write-a-skill Build a skill for remote service deployment and troubleshooting. It must support retries, logs, health checks, and safe rollback.
```

For complex workflows, it will grill the design first:

1. Trigger boundaries
2. Safety rails
3. Phase decomposition
4. BLOCK / ASK / LOG decisions
5. State and memory
6. Trajectory evaluation
7. Budget and degradation
8. Sub-agent needs

### Audit an existing skill

```text
/write-a-skill Audit my skill at ./skills/my-skill
```

It checks:

- description quality
- safety rails
- phase boundaries
- decision tables
- knowledge persistence
- progressive disclosure
- harness-level governance
- eval / trace / regression readiness

### Evolve a skill with test cases

```text
/write-a-skill Evolve ./skills/my-skill using ./eval/gt.dev.jsonl
```

The EVOLVE protocol uses:

- GT data
- dev / holdout / regression split
- per-case trace
- L1 / L2 / L3 evals
- five-dimensional AND gates
- checkpoint and rollback

---

## Example: IT deployment troubleshooting skill

See:

```text
examples/it-deploy-troubleshooting-skill/
```

This example demonstrates a realistic IT operations skill for:

- deployment step generation
- failed service troubleshooting
- health checks
- log and port investigation
- remote operation safety rails
- monitor-agent protocol
- GT cases for evolution

It is intentionally generic and does not include private infrastructure details.

---

## Who should use this

Use `write-a-skill` if you are:

- building Claude Code skills for internal engineering teams
- turning SOPs into executable agent workflows
- designing domain-specific AI agents
- making deployment, DevOps, security, or support automation reliable
- trying to avoid prompt sprawl
- looking for a repeatable way to audit and improve skills

---

## SEO keywords

Claude Code skills, Claude Code skill creator, AI agent skill framework, agentic workflow design, prompt engineering, context engineering, multi-agent harness, MCP tool governance, AI agent evaluation, LLM evals, Claude Code automation, DevOps AI agent, deployment troubleshooting AI, self-evolving agent skills.

---

## Star-worthy highlights

- Not just a template: a full skill engineering framework
- Works for simple and complex business workflows
- Includes audit checklist and good/bad patterns
- Adds harness thinking for production-grade agent workflows
- Adds EVOLVE protocol for self-improving skills
- Includes a realistic IT deployment troubleshooting example

---

## Roadmap ideas

- Add automated validation scripts for `SKILL.md`
- Add sample eval runner
- Add more domain examples: customer support, security triage, data pipeline ops
- Add GitHub Action for skill linting
- Add marketplace-style skill registry examples

---

## License

MIT
