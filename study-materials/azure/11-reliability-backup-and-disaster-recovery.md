# 11 — Reliability, Backup and Disaster Recovery

## Reliability is planned, not assumed

A cloud service can still fail. A region can experience disruption, a deployment can introduce a bug, credentials can expire, a database can be deleted, or a dependency can become throttled.

Design reliability around business impact and measurable recovery objectives.

## 1. Availability and resilience concepts

- **High availability (HA):** design to keep a workload operating through expected component failures.
- **Fault domain:** group of hardware that may fail together.
- **Availability zone:** physically separate location within a supported region.
- **Disaster recovery (DR):** ability to restore service after a major disruption.
- **RTO:** recovery time objective—the target time to restore service.
- **RPO:** recovery point objective—the target maximum data loss measured in time.

RTO and RPO are business decisions. They influence architecture, replication, backups, and cost.

## 2. Design for failure

Identify dependencies and ask what happens when each one is unavailable. Consider:
- Timeouts and bounded retries.
- Exponential backoff with jitter.
- Circuit breakers.
- Health probes.
- Queue-based load levelling.
- Redundant instances and availability zones where supported.
- Graceful degradation and cached non-sensitive data.
- Deployment rollback and configuration rollback.

A retry can make an outage worse if every instance retries aggressively. Retry only transient failures, cap attempts, and avoid retrying non-idempotent operations without safeguards.

## 3. Backup vs replication

Backup creates recovery points that can help restore deleted, corrupted, or changed data. Replication maintains copies of data or service capacity in another location, but corruption or deletion may also replicate depending on the design.

Neither is a complete substitute for the other. Define retention, access protection, encryption, and restore testing.

## 4. Azure recovery features

Different Azure services provide different backup, restore, replication, and failover capabilities. Examples include database point-in-time restore, storage redundancy options, and service-specific backup integrations.

Always verify the actual service tier, configuration, region pair behaviour, feature limits, and recovery process. Do not assume a default setting meets your RTO/RPO.

## 5. Runbooks and recovery tests

A runbook should state:
- What failed and how impact is identified.
- Who owns the decision to fail over.
- Which systems are restored and in what order.
- How DNS, secrets, identity, and network paths are validated.
- How data consistency is confirmed.
- How users are informed.
- How the system returns to normal operation.

Test restore procedures regularly. A backup that has never been restored is an unverified recovery assumption.

## Practice exercise

For a small business API using App Service, Azure SQL, Blob Storage, and Service Bus, propose RTO and RPO values as a discussion exercise. Identify each service's backup or recovery mechanism, dependencies, failover steps, and the tests needed to prove recovery. Mark all assumptions that need validation with service owners.

## Self-check

- How are RTO and RPO different?
- Why is replication not the same as backup?
- Why can retries amplify an outage?
- What is a circuit breaker intended to do?
- Why must restore procedures be tested?

## Official references

- Azure reliability: https://learn.microsoft.com/azure/reliability/
- Azure Well-Architected reliability: https://learn.microsoft.com/azure/well-architected/reliability/
- Azure Backup: https://learn.microsoft.com/azure/backup/
- Azure Site Recovery: https://learn.microsoft.com/azure/site-recovery/
