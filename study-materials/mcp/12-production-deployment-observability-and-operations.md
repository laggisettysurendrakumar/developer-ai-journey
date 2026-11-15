# 12 — Production Deployment, Observability, and Operations

## From demo to service

A local demo is not automatically a production service. Production readiness includes deployment, identity, configuration, resource limits, dependency health, telemetry, incident response, and safe upgrades.

## 1. Deployment shape

For a remote server, a typical design is:

```text
MCP Host / Client
       |
     HTTPS
       |
Ingress / API Gateway
       |
Authentication + Rate Limits
       |
MCP Server Instances
       |
Application Services
       |
Approved APIs / Databases
```

The gateway is not a replacement for object-level authorisation in the application service.

## 2. Configuration and secrets

Keep non-secret configuration separate from secret material. Use environment-specific configuration and a managed secret store. Validate required settings at startup. Never commit tokens or private endpoints to source control.

Define:
- allowed origins,
- authentication issuer and audience,
- downstream endpoints,
- timeout and concurrency limits,
- maximum input/output size,
- feature flags,
- log and audit retention.

## 3. Health and readiness

A liveness check should indicate whether the process is alive and able to make progress. A readiness check should indicate whether the instance can safely receive requests. Avoid making readiness depend on every optional dependency if that would cause unnecessary fleet-wide removal during a partial outage.

## 4. Logs, metrics, traces

Logs explain individual events. Metrics show aggregate behaviour. Traces show where a request spent time across services.

Track:
- request count by tool,
- successful and failed calls,
- latency percentiles,
- timeouts and cancellation,
- backend dependency latency,
- throttling and rejected requests,
- active connections and queue depth,
- authentication/authorisation denials,
- resource size and token-related cost where applicable.

Use correlation IDs and distributed tracing. Redact tokens, secrets, and sensitive payloads.

## 5. Capacity and resilience

Set limits for CPU, memory, request concurrency, queue size, output size, and downstream connection pools. Load-test realistic workloads. Use graceful shutdown and drain in-flight requests during deployment.

## 6. Release strategy

- pin SDK and dependency versions,
- review protocol compatibility,
- run unit, contract, security, and integration tests,
- deploy to a test environment,
- test with representative hosts,
- canary a small traffic share where possible,
- monitor error/latency trends,
- roll back on defined thresholds.

Do not silently upgrade an SDK or protocol version in production.

## 7. Incident runbook outline

For “tool calls are failing”:
1. Confirm incident scope and start time.
2. Check service health and recent deployment/configuration changes.
3. Break down failures by tool, host, transport, and dependency.
4. Check authentication, rate limits, and connection errors.
5. Inspect traces for slow or failing dependencies.
6. Protect backend systems by reducing concurrency or disabling only the affected capability when approved.
7. Verify recovery with safe read-only calls.
8. Record impact, timeline, root cause, and follow-up actions.

## Practice exercise

Write an operations dashboard specification for your MCP server. Include five service-level indicators, alert thresholds to be chosen from a baseline, and a runbook for a downstream database outage. Explain how you would deploy a new server version without interrupting active clients.

## Self-check

1. What is the difference between liveness and readiness?
2. Why use metrics and traces as well as logs?
3. Why should an API gateway not be the only authorisation layer?
4. What should be pinned before a release?
5. What evidence should be captured during an MCP incident?

## Further reading

- [MCP security best practices](https://modelcontextprotocol.io/docs/tutorials/security/security_best_practices)
- [OpenTelemetry](https://opentelemetry.io/docs/)
