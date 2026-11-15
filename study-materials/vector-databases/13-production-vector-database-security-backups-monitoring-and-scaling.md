# Production Vector Databases: Security, Backups, Monitoring, and Scaling

## 1. Moving from a local demo to production

A local vector database is useful for learning. A production system must also handle failures, data growth, access boundaries, backups, upgrades, monitoring, and cost.

A vector database is part of a larger application architecture. It usually stores derived representations of source documents. The source system remains important because the original content, permissions, and document lifecycle may live elsewhere.

## 2. Production architecture

A typical architecture contains:

1. **Source systems:** knowledge bases, file storage, databases, or application APIs.
2. **Ingestion workers:** fetch, validate, extract, chunk, and normalise documents.
3. **Embedding service:** converts document chunks and queries to vectors.
4. **Vector database:** stores vectors, IDs, and metadata.
5. **Search API:** authenticates callers, applies access rules, and executes retrieval.
6. **Optional keyword engine:** supports exact terms and hybrid retrieval.
7. **Optional reranker:** improves ordering of retrieved candidates.
8. **Observability platform:** collects logs, metrics, traces, and alerts.

In a .NET environment, ASP.NET Core can host the search API and background workers. The embedding service and vector database can be hidden behind interfaces so that controllers do not depend directly on one vendor's SDK.

## 3. Separate source of truth from derived vectors

Embeddings are derived data. I should be able to rebuild them from the source documents and the processing configuration.

Track at least:

- Source document ID.
- Source version or ETag.
- Chunk ID and chunk number.
- Embedding model and model version.
- Embedding dimensions.
- Chunking configuration version.
- Ingestion status and timestamp.
- Tenant and access metadata.

If the embedding model changes, vectors from the old and new model should not be mixed in the same vector space unless the model provider explicitly supports compatibility. A common migration approach is to build a new collection or vector field, validate it, then switch traffic.

## 4. Security checklist

### Identity and access

- Authenticate application services using managed identity or an appropriate service identity where supported.
- Store API keys and passwords in a secret manager, not in source control.
- Use separate credentials for development, staging, and production.
- Grant only the minimum required permissions.
- Rotate credentials and review access periodically.

### Network security

- Keep the vector database on a private network where possible.
- Do not expose administrative ports directly to the public internet.
- Restrict inbound access to known application networks.
- Use TLS for traffic across untrusted network boundaries.
- Apply firewall and private endpoint controls according to the hosting platform.

### Tenant isolation and data protection

- Derive tenant identity from authenticated server context.
- Apply tenant and document permissions to every retrieval path.
- Test cross-tenant access explicitly.
- Avoid logging full document text, API keys, or sensitive payloads.
- Define retention and deletion requirements for vectors and derived content.
- Review whether stored text or embeddings are considered sensitive data in the organisation's policies.

An embedding is not a replacement for the source document, but it should not automatically be treated as harmless or public. Protect it according to the data classification and threat model.

## 5. Backups and recovery

A backup is useful only when restoration has been tested.

I should document:

- What data must be backed up.
- Whether snapshots are consistent with the database's durability guarantees.
- Backup frequency and retention.
- Where backup copies are stored.
- Encryption and access restrictions.
- Recovery Point Objective (RPO): how much recent data the organisation can afford to lose.
- Recovery Time Objective (RTO): how long service can be unavailable.
- Restore validation steps.

A production design may also preserve the original source documents and the ingestion manifest so that vectors can be rebuilt if required.

Do not assume that copying a live database directory is a safe backup. Follow the database vendor's supported snapshot and backup procedure for the selected deployment mode.

## 6. Monitoring and service objectives

Useful metrics include:

| Area | Example signal | Why it matters |
|---|---|---|
| Availability | Successful health checks | Is the service reachable? |
| Search latency | p50, p95, p99 | Are users waiting too long? |
| Error rate | Failed searches and timeouts | Is the service reliable? |
| Throughput | Requests per second | Can it handle load? |
| Retrieval quality | Recall@K, nDCG, MRR | Are results relevant? |
| Resource use | CPU, memory, disk | Is capacity sufficient? |
| Index state | Build or optimisation status | Is search ready? |
| Ingestion | Queue depth and processing lag | Are changes being indexed? |
| Embedding | Provider latency and failures | Is ingestion/search blocked? |

Track both database latency and end-to-end API latency. Embedding generation, network calls, metadata filtering, reranking, and response generation can all contribute to total request time.

Use correlation IDs so a slow user request can be traced across the API, embedding provider, and database.

## 7. Scaling options

Scaling is a workload decision. Depending on the database and deployment, options may include:

- Vertical scaling by adding CPU, memory, or faster storage.
- Replicas for availability or read capacity.
- Sharding or partitioning to spread data and workload.
- Separate collections or namespaces for different workloads.
- Dedicated ingestion workers to isolate write traffic.
- Caching for repeated query embeddings or common results.
- Batch upsert to improve ingestion throughput.

Each option introduces trade-offs. Replication may increase storage cost. Sharding adds operational complexity. Caching can return stale data unless invalidation is designed carefully.

## 8. Capacity planning

Estimate the number of records, vector dimensions, metadata size, growth rate, query concurrency, and retention period.

A simple planning checklist:

- How many source documents exist?
- How many chunks does each document produce on average?
- What is the vector dimension and numeric representation?
- How much payload metadata is stored?
- How fast are documents added or updated?
- What is the expected query rate and concurrency?
- How long must deleted or old versions be retained?
- What backup and replica overhead is required?

Use a representative benchmark to measure actual storage and memory use. A rough vector-size calculation alone will omit index, metadata, replication, and database overhead.

## 9. Deployment and change management

Before changing a production collection or database version:

1. Review vendor release notes and compatibility.
2. Back up data using the supported process.
3. Test the upgrade or migration in staging.
4. Verify client SDK and server compatibility.
5. Measure retrieval quality and query latency.
6. Define rollback steps.
7. Monitor after release.

Schema or embedding changes should be versioned. A collection migration should include a validation phase and a controlled traffic switch.

## 10. Incident response examples

**Search latency increases:** check query volume, filter patterns, resource pressure, index state, network latency, and recent releases. Compare p95 and p99 with the baseline.

**No results are returned:** check collection name, embedding model, vector dimensions, filters, tenant context, and ingestion status.

**New documents are missing:** inspect the ingestion queue, source fetch, parsing errors, embedding provider, upsert result, and indexing status.

**Possible cross-tenant result:** treat it as a security incident. Preserve evidence, restrict affected access, investigate logs, assess exposure, and follow the organisation's incident process.

## Practice exercise

1. Draw a production architecture for an ASP.NET Core search API and a vector database.
2. Identify the source of truth for documents and permissions.
3. Write a tenant isolation test plan.
4. Define RPO and RTO for a business knowledge assistant.
5. Choose five metrics and define alert conditions based on expected traffic.
6. Write a backup and restore runbook.
7. Design an embedding migration from model version A to model version B.
8. Create an incident checklist for missing documents and slow searches.

## Self-check

- Why should the source system remain the source of truth?
- What information should be tracked with each ingested chunk?
- Why are embeddings and payloads still security-relevant?
- What is the difference between RPO and RTO?
- Why should backups be restored in a test?
- What should be monitored beyond database CPU and memory?
- What trade-offs come with replicas and sharding?
- Why should embedding migrations use a controlled rollout?

## Key takeaway

Production readiness is not only about fast vector queries. It includes secure access, recoverable data, reliable ingestion, measurable relevance, observability, and controlled change management.
