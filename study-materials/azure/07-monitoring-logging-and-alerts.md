# 07 — Monitoring, Logging and Alerts

## Why production monitoring matters

A deployment can be healthy while users are experiencing errors. A process can be running but unable to connect to its database. An API can return HTTP 200 while producing incorrect results.

Monitoring gives the team evidence about application behaviour and helps detect incidents before they become larger problems.

## 1. The three common telemetry types

- **Metrics:** numerical measurements over time, such as request count, CPU, memory, and latency.
- **Logs:** timestamped events that explain what happened.
- **Traces:** connected operations showing how one request travelled across services and dependencies.

Use all three. A metric can show that latency increased; traces can identify which dependency was slow; logs can explain the failure details.

## 2. Azure Monitor and Application Insights

Azure Monitor is the platform for collecting and analysing telemetry from Azure resources and applications. Application Insights provides application performance monitoring capabilities.

Learn:
- Workspace-based Application Insights.
- Connection configuration and instrumentation.
- Requests, dependencies, exceptions, and custom events.
- Distributed tracing and correlation.
- Log Analytics workspaces and Kusto Query Language (KQL).
- Alerts, action groups, dashboards, and workbooks.

The exact SDK and instrumentation approach depends on application runtime and hosting model. Follow current .NET guidance rather than copying an old package setup without checking compatibility.

## 3. Useful application signals

For a web API:
- Request rate.
- Error rate and exception count.
- p50, p95, and p99 latency.
- Dependency duration and failure.
- Availability tests.
- Queue depth and processing age.
- CPU, memory, restarts, and throttling.
- Deployment version and environment.

For a GenAI application, consider additional signals:
- Model request duration and failure rate.
- Token consumption and estimated cost.
- Retrieval latency and result count.
- Tool invocation success/failure.
- Safety and policy outcomes.
- User feedback and answer-quality evaluation.

Never collect more prompt or document content than necessary. Apply access controls, redaction, retention, and data-classification rules to telemetry.

## 4. KQL introduction

Kusto Query Language is used to query data in Azure Monitor Logs and related environments.

A conceptual query:

```kusto
requests
| where timestamp > ago(24h)
| summarize RequestCount = count() by resultCode
| order by RequestCount desc
```

Read it from top to bottom:
- Start with request records.
- Keep records from the last 24 hours.
- Group by response code and count rows.
- Sort the result from highest count to lowest.

Table names and available columns depend on the data source and schema.

## 5. Alerts that lead to action

An alert should have:
- A meaningful condition and threshold.
- An evaluation window.
- A severity.
- An action group or notification destination.
- An owner and runbook.
- A way to reduce duplicate or noisy notifications.

Alert on user impact and meaningful resource saturation, not every harmless fluctuation. Test notification routes before relying on them.

## 6. Incident workflow

When an alert fires:
1. Confirm the affected environment and time window.
2. Check recent deployment and configuration changes.
3. Look at request and dependency failure rates.
4. Follow a representative distributed trace.
5. Check resource pressure, quota, and throttling.
6. Identify mitigation and communicate impact.
7. Record root cause and follow-up actions.

Preserve correlation IDs across application boundaries. They make it easier to connect logs and traces from multiple services.

## Practice exercise

Instrument a sample ASP.NET Core API. Generate successful requests and controlled failures. Create a query that counts requests by status code and a second query that investigates slow dependencies. Create a test alert and verify the notification path.

## Self-check

- What is the difference between a metric, log, and trace?
- Why is p95 latency often more informative than average latency alone?
- What does a correlation ID help you do?
- Which GenAI-specific signals are useful to track?
- Why should prompts and document text be handled carefully in telemetry?

## Official references

- Azure Monitor: https://learn.microsoft.com/azure/azure-monitor/
- Application Insights: https://learn.microsoft.com/azure/azure-monitor/app/app-insights-overview
- Log Analytics: https://learn.microsoft.com/azure/azure-monitor/logs/log-analytics-overview
- KQL: https://learn.microsoft.com/azure/data-explorer/kusto/query/
- Alerts: https://learn.microsoft.com/azure/azure-monitor/alerts/alerts-overview
