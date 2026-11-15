# 05 — Storage, Databases and Messaging

## Start with the type of data and access pattern

Applications use different data services for different jobs. Choosing a database only because it is familiar can create unnecessary cost or operational complexity.

Ask:
- Is the data relational or document/key-value shaped?
- Do I need transactions and joins?
- What are the read/write patterns and data volume?
- How much latency can the application tolerate?
- What are the availability, backup, retention, and recovery needs?
- Does the workload need asynchronous messaging?

## 1. Azure Storage

Azure Storage offers services such as Blob, Files, Queues, and Tables.

**Blob Storage** is commonly used for unstructured objects such as documents, images, exports, and backups. Learn:
- Storage account and container.
- Blob naming and metadata.
- Access tiers and lifecycle policies.
- Redundancy options.
- Shared access signatures (SAS) and identity-based access.
- Soft delete, versioning, and retention controls where configured.

Prefer identity-based access when supported. A SAS token is a bearer credential: anyone who obtains it may use its permissions until it expires or is revoked through the relevant mechanism. Keep its scope and lifetime narrow.

## 2. Relational data with Azure SQL

Azure SQL provides managed SQL database capabilities. For a .NET developer, the programming model is familiar: tables, constraints, indexes, queries, transactions, and connection strings.

Understand:
- Logical server and database concepts.
- Authentication methods, including Microsoft Entra authentication.
- Firewall and network configuration.
- Compute and storage purchasing models.
- Backups, point-in-time restore, and long-term retention options.
- Indexes, query plans, and performance tuning.
- High availability and disaster recovery features.

Production database capacity should be based on workload measurements and testing, not guesswork. Confirm service tier, regional support, backups, and recovery objectives.

## 3. Cosmos DB and other data models

Azure Cosmos DB is a distributed database platform with APIs and consistency options. It may fit globally distributed applications or specific low-latency, high-scale access patterns.

Learn partition keys early: a poor partition strategy can create hot partitions and uneven throughput. Understand request units (RUs) for relevant API/account configurations, consistency levels, indexing, change feed, and backup options.

Do not use Cosmos DB just because an application stores JSON. Compare its access model, partition design, consistency, cost, and operational fit with relational databases and other stores.

## 4. Messaging: queues and eventing

Messaging helps components communicate without requiring every step to complete within one HTTP request.

- Azure Queue Storage: simple queue storage.
- Azure Service Bus: enterprise messaging features such as queues, topics, subscriptions, and dead-lettering.
- Azure Event Grid: event distribution for event-driven architectures.
- Azure Event Hubs: high-throughput event ingestion and streaming.

Choose based on delivery semantics, ordering, routing, throughput, fan-out, retention, and consumer behaviour.

## 5. Reliability patterns for messaging

A consumer should expect duplicate deliveries in many distributed messaging designs. Build idempotent processing: receiving the same message twice must not produce an incorrect duplicate effect.

Learn:
- Retry and exponential backoff.
- Dead-letter queues.
- Message lock/visibility and settlement.
- Duplicate detection where available.
- Poison messages and replay procedures.
- Outbox pattern for reliable database-to-message publication.
- Schema versioning and compatibility.

Never assume that an HTTP request and a queue publish form one atomic transaction unless the system explicitly provides that guarantee.

## 6. Data security

Classify data before selecting access and retention:
- Public.
- Internal.
- Confidential.
- Regulated or sensitive, according to organisational policy.

Use encryption in transit and at rest, scoped identities, private connectivity where required, auditing, retention limits, backup access controls, and deletion workflows. Avoid writing raw secrets, access tokens, or sensitive documents into logs.

## Practice exercise

Design a support application that stores user accounts, conversation metadata, uploaded manuals, and background indexing jobs. Choose a service for each data type, justify the choice, sketch the data flow, and describe how a duplicate indexing message is handled safely.

## Self-check

- When is Azure SQL a more natural choice than Blob Storage?
- Why is a Cosmos DB partition key important?
- Which problem does a queue solve that a direct HTTP call may not?
- What is a dead-letter queue?
- What does idempotent message processing mean?

## Official references

- Azure Storage: https://learn.microsoft.com/azure/storage/
- Blob Storage: https://learn.microsoft.com/azure/storage/blobs/storage-blobs-introduction
- Azure SQL: https://learn.microsoft.com/azure/azure-sql/
- Cosmos DB: https://learn.microsoft.com/azure/cosmos-db/
- Service Bus: https://learn.microsoft.com/azure/service-bus-messaging/
- Event Grid: https://learn.microsoft.com/azure/event-grid/
- Event Hubs: https://learn.microsoft.com/azure/event-hubs/
