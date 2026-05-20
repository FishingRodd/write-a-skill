# Harness Notes

This example touches remote systems and deployment tools, so it uses a lightweight harness boundary.

## Tool Governance

| Operation | Risk | Rule |
|-----------|------|------|
| read logs/config | low | allowed |
| restart service | medium | allowed after root cause is identified |
| overwrite config | medium | allowed if reversible and backed by diff |
| delete data/reset state | high | BLOCK unless explicitly authorized |
| run destructive cleanup | high | BLOCK unless explicitly authorized |

## Trace

Record:

- command executed
- output summary
- changed files
- service state before/after
- verification result

## Stop Conditions

Stop when:

- credentials are missing
- destructive action is required
- service remains unhealthy after the same fix is tried twice
- root cause contradicts current assumptions
