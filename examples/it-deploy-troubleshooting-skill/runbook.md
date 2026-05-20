# Deployment Troubleshooting Runbook

## 1. Collect Context

Gather only the information needed for the current failure:

- deployment command output
- service status
- recent logs
- rendered/generated config
- listening ports
- CPU architecture and OS version
- container/runtime version when relevant

## 2. Classify Failure

| Symptom | Likely Cause | First Check |
|---------|--------------|-------------|
| process exits immediately | config or permission error | service logs |
| port not listening | process failed or wrong bind address | service status + config |
| health check timeout | dependency unavailable or slow startup | dependency logs |
| binary fails to run | architecture/runtime mismatch | `uname -m`, binary metadata |
| config rendered but invalid | template/schema error | generated config syntax |

## 3. Fix Minimally

- Change the smallest file or parameter that explains the failure.
- Avoid broad refactors during incident recovery.
- Do not change unrelated services.

## 4. Verify

A deployment is not complete until all pass:

- service process is running
- expected ports are listening
- health check passes
- logs show no repeating errors
- config on disk matches expected values

## 5. Record Pitfall

If the issue is reusable, add it to `pitfalls.md` using:

```md
## Short title

**Symptom**: ...
**Cause**: ...
**Fix**: ...
**Verify**: ...
```
