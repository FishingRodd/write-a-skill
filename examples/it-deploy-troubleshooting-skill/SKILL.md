---
name: it-deploy-troubleshooting
description: Design deployment steps and troubleshoot failed service deployments in a generic IT environment. Use when: creating deployment scripts, debugging failed installs, checking service health, investigating ports/logs/configs, or resuming interrupted deployments. Do NOT use for: application feature development without deployment or operations context.
---

# IT Deploy Troubleshooting Skill

## Overview

| Mode | Trigger | Entry |
|------|---------|-------|
| **CREATE** | Create deployment steps for a service | → Phase 1 |
| **TROUBLESHOOT** | Deployment failed or service unhealthy | → Phase 2 |
| **RESUME** | Interrupted deployment should continue safely | → Phase 3 |

---

## Phase 1: Create Deployment Steps

**Goal**: produce minimal, repeatable deployment steps.

**Actions**:
- Read existing scripts, manifests, Dockerfiles, Helm charts, and config examples first.
- Reuse the repository's layout, naming, and operational style.
- Define install, configure, start, health-check, and rollback/resume behavior.

**Completion criteria**:
- Deployment steps are runnable.
- Required configs, assets, ports, and services are documented.
- Health checks are explicit.

---

## Phase 2: Troubleshoot Deployment Failure

**Goal**: identify the smallest root cause and fix it.

**Actions**:
- Collect service status, logs, ports, generated configs, and recent deployment output.
- Classify failure: config error, missing asset, port conflict, permission issue, architecture mismatch, dependency failure, or health-check timeout.
- Apply the smallest safe fix.
- Re-run only the necessary deployment or verification step.

**Completion criteria**:
- Service process is running.
- Required ports are listening.
- Health endpoint or smoke command passes.
- Root cause and fix are recorded.

---

## Phase 3: Resume Interrupted Deployment

**Goal**: continue safely without corrupting existing state.

**Actions**:
- Determine the last completed step.
- Check idempotency of the next step.
- Avoid deleting data or resetting state unless explicitly authorized.
- Resume from the safest verified point.

**Completion criteria**:
- Deployment reaches healthy state.
- No destructive recovery was performed without authorization.

---

## BLOCK / ASK / LOG Decision Table

| Situation | Decision | Action |
|-----------|----------|--------|
| Operation would delete data, reset state, or overwrite production config | **BLOCK** | Stop and require explicit authorization |
| Credentials or remote access are unavailable | **BLOCK** | State the missing access and stop |
| Multiple safe fixes are possible | **ASK** | Recommend one path and wait for selection |
| Logs show a known pitfall | **LOG** | Apply the documented fix and report it |
| A non-blocking issue is discovered | **LOG** | Record it as follow-up; do not expand scope |

---

## Safety Rails

**NEVER**:
- Never delete service data or reset production state without explicit authorization.
- Never skip verification after modifying deployment scripts or configs.
- Never rewrite the deployment framework when a minimal fix is enough.
- Never assume architecture compatibility; verify CPU architecture and runtime dependencies.

**ALWAYS**:
- Read existing implementation before editing.
- Prefer local generation + file transfer over fragile inline remote heredocs.
- Verify process, port, config, and logs after deployment.
- Keep fixes minimal and reversible.

---

## Supporting Files

| File | Purpose | Read when |
|------|---------|-----------|
| `runbook.md` | Full troubleshooting workflow | Debugging a failed deployment |
| `monitor-agent.md` | Background monitor behavior | Long-running deployment needs monitoring |
| `harness.md` | Tool governance, trace, and stop conditions | High-risk or remote operations are involved |
| `pitfalls.md` | Known deployment pitfalls | Error matches a known pattern |
| `eval/` | Ground truth cases for self-evolution | Testing or improving this skill |
