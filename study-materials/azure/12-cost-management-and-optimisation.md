# 12 — Cost Management and Optimisation

## Cloud cost is part of architecture

Cloud resources are easy to create, and that convenience can lead to idle or oversized services. Cost should be reviewed during design, deployment, and regular operations.

The price of a workload is influenced by compute size and runtime, storage capacity and transactions, data transfer, logs retained, database capacity, model tokens, network services, and redundancy choices.

## 1. Learn the billing model

Different services use different billing meters. Some charge for provisioned capacity; others charge by execution, storage, requests, throughput, or consumption.

Before deployment:
1. Identify all resources and their likely meters.
2. Use the official pricing calculator for an estimate.
3. Identify minimum fixed costs.
4. Estimate normal and peak usage.
5. Include monitoring, backup, networking, and data transfer.
6. Set budget notifications where available.
7. Review actual consumption after the lab.

An estimate is not a quote. Pricing varies by region, plan, currency, agreement, and usage.

## 2. Cost controls

- Tag resources for ownership and cost allocation.
- Set budgets and alert thresholds.
- Right-size compute using measurements.
- Stop or remove unused lab resources.
- Use storage lifecycle policies where appropriate.
- Review log ingestion and retention.
- Understand autoscaling minimums and maximums.
- Set model token and request limits.
- Review reservations or savings options only after understanding stable long-term usage.

Budgets and alerts notify teams; they do not necessarily stop spending automatically. Do not treat a budget as a hard spending cap unless the specific control explicitly provides that behaviour.

## 3. GenAI cost drivers

For model-backed applications, track:
- Input and output tokens.
- Model choice and deployment.
- Number of model calls per user request.
- Context window size.
- Retries and failed calls.
- Embedding and indexing volume.
- Search or vector service capacity.
- Cached result hit rate.

A multi-step agent may make several model and tool calls for one user request. Put limits on steps, tokens, duration, and tool usage.

## 4. Cost optimisation method

1. Measure current cost by resource and workload.
2. Identify the largest contributors.
3. Check utilisation and service limits.
4. Change one variable at a time.
5. Run performance and reliability tests.
6. Compare cost per successful workload, not just the monthly bill.
7. Document any trade-offs in availability or latency.

## Practice exercise

Create a monthly cost estimate for a learning API, database, storage account, monitoring workspace, and model endpoint. Use the Azure Pricing Calculator and record assumptions, region, usage volume, and what is excluded. Then identify three ways to reduce cost without weakening security or reliability.

## Self-check

- Why can a budget alert not be assumed to stop spending?
- What are common fixed and usage-based cost drivers?
- Why should cost per successful request be measured?
- How do logs and agent retries affect cost?
- Why is right-sizing based on measured usage?

## Official references

- Azure pricing calculator: https://azure.microsoft.com/pricing/calculator/
- Cost Management: https://learn.microsoft.com/azure/cost-management-billing/
- Azure Well-Architected cost optimisation: https://learn.microsoft.com/azure/well-architected/cost-optimization/
