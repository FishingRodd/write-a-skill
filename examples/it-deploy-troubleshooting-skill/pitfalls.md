# Deployment Pitfalls

## Architecture mismatch

**Symptom**: binary or container fails at startup with an exec/runtime error.
**Cause**: artifact built for a different CPU architecture or runtime.
**Fix**: use the correct architecture-specific artifact.
**Verify**: service starts and health check passes.

## Port conflict

**Symptom**: service fails to bind or port remains unavailable.
**Cause**: another process already owns the port.
**Fix**: identify the owner and change config or stop the conflicting service safely.
**Verify**: expected process owns the port.

## Invalid rendered config

**Symptom**: service exits immediately after config generation.
**Cause**: template rendered invalid syntax or missing required fields.
**Fix**: validate generated config before starting the service.
**Verify**: syntax validation and service startup both pass.
