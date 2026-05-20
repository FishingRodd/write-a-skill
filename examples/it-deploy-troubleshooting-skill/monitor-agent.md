# Monitor Agent Protocol

Use a background monitor agent only for long-running deployments.

## Inputs

- deployment log path
- expected service names
- expected ports
- health check command or URL
- maximum idle time

## Detect

| Signal | Meaning | Action |
|--------|---------|--------|
| no log output for too long | possible stall | report STALL_SILENT |
| repeated health-check failure | service unhealthy | report STALL_HEALTH |
| explicit error pattern | deployment failed | report DEPLOY_ERROR |
| all checks pass | deployment complete | report DEPLOY_OK |

## Rules

**NEVER**:
- Never modify files.
- Never restart services.
- Never kill processes.

**ALWAYS**:
- Observe only.
- Report concise findings.
- Include exact log snippets and timestamps when possible.
