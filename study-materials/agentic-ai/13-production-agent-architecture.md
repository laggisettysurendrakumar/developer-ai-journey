# Lesson 13 — Production Agent Architecture and Deployment

## Learning goals

Move from a local demo to an application that can be operated, secured, monitored, and maintained. A production agent is a software system, not just a model and a prompt.

## 1. Example architecture

```text
Web / UI
   |
   v
API gateway / authentication
   |
   v
Python application service
   |
   +--> Input validation and policy checks
   |
   +--> Agent runner / workflow engine
   |       |
   |       +--> Model provider
   |       +--> Approved tool registry
   |       +--> RAG retrieval service
   |
   +--> State/session store
   |
   +--> Approval service for side effects
   |
   +--> Logs, traces, metrics, evaluation
```

The model provider is one dependency. The application owns identity, data, permissions, and business rules.

## 2. Separate responsibilities

Suggested components:
- `api`: HTTP routes and request/response contracts.
- `agent`: instructions, orchestration, and run limits.
- `tools`: narrow functions and external-service adapters.
- `security`: identity, authorisation, validation, and approvals.
- `memory`: session persistence and retention.
- `retrieval`: document ingestion and search.
- `observability`: traces, logs, and metrics.
- `evaluation`: datasets and automated checks.

Small components are easier to test and replace.

## 3. API boundary

A web API should authenticate the caller, validate input, assign a request ID, enforce rate limits, and pass trusted identity context into the agent service.

The agent should not invent a user ID or tenant from prompt text. The backend should obtain it from verified authentication claims.

## 4. Background execution

Some tasks can outlast an HTTP request. In that case:
1. API validates and creates a run.
2. Queue stores a run ID and approved task parameters.
3. Worker loads the run and verifies ownership.
4. Worker executes bounded steps.
5. Progress is persisted.
6. User polls or receives a completion event.

Make duplicate queue delivery safe with idempotency and durable checkpoints.

## 5. Data and persistence

Possible data stores:
- Relational DB for users, runs, approvals, and metadata.
- Object storage for source documents.
- Vector/search store for embeddings and retrieval.
- Cache for short-lived state where appropriate.

Choose persistence only where needed. Define tenant isolation, encryption, backup, deletion, and retention for each store.

## 6. Deploy with Docker first

Package the application using the Docker course. Use an approved base image, pin dependencies, run non-root, inject secrets at runtime, expose health endpoints, and scan the image.

Do not introduce Kubernetes unless the application requires its scheduling, scaling, or operational capabilities.

## 7. Reliability and operations

Monitor:
- Success/failure rate by operation.
- Model and tool latency.
- Tool-call distribution.
- Token usage and cost.
- Queue depth and worker restarts.
- Approval wait time.
- Retrieval quality and no-answer rate.
- Safety blocks and permission denials.

Create alerts for sustained failures, unusual cost, and tool error spikes.

## 8. Release strategy

- Keep prompts, schemas, models, and tools versioned.
- Run unit and integration tests.
- Run the agent evaluation set before deployment.
- Use staging and a limited rollout.
- Keep rollback procedures.
- Review permission changes separately from prompt changes.

## 9. Practice project

Create a production-style design for the Developer Support Agent:
1. FastAPI endpoint protected by authentication (mocked in the learning project).
2. Agent service with a maximum step count.
3. Read-only search/status tools.
4. Persistent run record and owner.
5. Approval workflow for any hypothetical write tool.
6. Structured logs and traces.
7. Evaluation pipeline.
8. Docker deployment.
9. Architecture diagram and threat model.

Do not connect the lab to production credentials or customer data.


## Practice exercise

Create a production-style design for the Developer Support Agent:
1. FastAPI endpoint protected by authentication (mocked in the learning project).
2. Agent service with a maximum step count.
3. Read-only search/status tools.
4. Persistent run record and owner.
5. Approval workflow for any hypothetical write tool.
6. Structured logs and traces.
7. Evaluation pipeline.
8. Docker deployment.
9. Architecture diagram and threat model.

Do not connect the lab to production credentials or customer data.


## Self-check

1. Why should authentication happen before the agent runner?
2. Why is a durable run ID useful?
3. How do queues help long-running tasks?
4. Why is rollback part of deployment design?
5. Which metrics help detect runaway agent cost?

**Completion check:** I can describe the components needed to operate an agent as a real application.
