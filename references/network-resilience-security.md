# Network Resilience and Security

## Resilience

- Define explicit connection, read, write, and request timeout policy.
- Retry only transient failures and operations safe to retry.
- Use bounded retries with exponential backoff and jitter when appropriate.
- Respect `Retry-After` when the protocol or API provides it.
- Avoid infinite retry loops.
- Do not blindly retry authentication failures, validation errors, or most client errors.
- Use idempotency keys or equivalent safeguards for repeated writes.
- Define connectivity transition behavior and cache fallback.
- Use circuit-breaker behavior only when system scale and failure modes warrant it.

## Authentication

- Keep tokens and credentials out of UI state.
- Centralize authentication handling.
- Use single-flight token refresh to prevent request storms.
- Prevent refresh loops.
- Retry the original request only according to a bounded policy.
- Model logout and session-expired state explicitly.
- Avoid token leakage through logs, exceptions, analytics, and crash reports.

## Security

- Never hardcode secrets.
- Never log tokens, passwords, full authorization headers, or sensitive payloads.
- Redact sensitive fields at logging boundaries.
- Use platform security facilities for credential storage.
- Keep TLS certificate and hostname verification enabled.
- Do not disable certificate or hostname verification to work around test or staging issues.
- Add certificate pinning only when required and with a rotation strategy.
- Treat server messages as untrusted display input.
- Avoid exposing internal exception details directly to users.

## Error Mapping

Map transport and server failures into domain-oriented categories such as offline, timeout, unauthorized, forbidden, not found, validation, rate limited, server unavailable, and unknown. Use the project's existing error model when coherent; do not invent parallel categories.
