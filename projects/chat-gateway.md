---
type: project
slug: chat-gateway
name: Chat Gateway
employer: Farsight AI
role: primary contributor and feature owner
period: 2025-12 — 2026-06
status: active
commits_by_kairi: 53
primary_languages: [Python]
technologies: [FastAPI, PostgreSQL, SQLAlchemy, Alembic, AWS SQS, AWS S3, AWS SSM, Boto3, aioboto3, Celery, Docker, AWS CDK, Anthropic Bedrock, OpenAI, SSE (server-sent events), Ruff, Pytest]
domains: [chat persistence, async messaging, deliverables pipeline, domain-driven design, event-driven architecture, infrastructure-as-code]
visibility: internal
---

# Chat Gateway

## What it is
A multi-tenant chat persistence and deliverables orchestration service built with FastAPI and PostgreSQL, following Domain-Driven Design. The service stored chat conversations organized as Projects → Chats → Messages, streamed LLM responses via SSE, and acted as the bridge between a frontend chat interface and a downstream NLP-triggered deliverables pipeline backed by AWS SQS.

## My role & ownership
Primary contributor across the service's most critical reliability and scalability work. Owned the complete SQS consumer subsystem from infrastructure to application layer, the DeliverableService refactor, and the silent turn-loss reliability fix. Drove the architectural decisions for async event processing and the multi-consumer concurrency model.

## Key contributions
- Diagnosed and fixed silent chat turn loss on client disconnect (DEV-1151): persisted turns to the database before streaming began so abrupt disconnects no longer dropped conversation history
- Built the end-to-end SQS consumer pipeline from scratch: SQS client, message router, progress and final-result transformers, liveness health-check entrypoint, and a dedicated Fargate ECS service with CDK wiring and IAM scoping
- Refactored `DeliverableCreationService` into `DeliverableService`, extracting submission, SQS publishing, and database commit into a clean orchestration layer that eliminated dead orphan-event code paths
- Implemented SQS publisher service and message queue abstraction for the deliverables submit flow (POST /deliverables)
- Added Parameter Store integration for SQS queue URLs, S3 bucket provisioning for deliverable I/O files, and IAM task-role permissions scoped to least-privilege
- Enabled multiple concurrent SQS consumers safely (DEV-748), resolving a queue-contention bottleneck
- Fixed blocking sync I/O in the async Excel conversion endpoint by dispatching file conversion to a threadpool, preventing event loop stalls
- Added CI ruff lint gate on PRs and resolved all pre-existing violations

## Technologies & patterns
- **DDD layering**: domain entities as pure dataclasses with behavior methods; abstract repository interfaces in `domain/repositories/`; implementations in `infrastructure/persistence/repositories/`
- **Event-driven async**: SQS publisher/consumer pattern with a message router dispatching to typed transformers (progress events vs. final results)
- **SSE streaming**: server-sent events via `sse-starlette` with `ChunkAggregationService` parsing for LLM proxy responses
- **Infrastructure as code**: AWS CDK TypeScript stacks for ECS Fargate services, S3, IAM roles, and Parameter Store hierarchies (`/{app}/{stage}/{param}`)
- **Async safety**: threadpool offload for blocking file I/O; auditry pinned to 0.2.5 to avoid a streaming-wedge regression

## Resume-ready bullets
- Eliminated silent chat turn loss on client disconnect by restructuring the persistence write to occur before SSE streaming, hardening data durability for all conversation turns
- Designed and shipped a full SQS consumer microservice — SQS client, typed message router, event transformers, Fargate ECS service, CDK infrastructure, and IAM scoping — enabling asynchronous NLP-triggered deliverable job processing
- Refactored `DeliverableCreationService` into a clean orchestration layer (`DeliverableService`), removing dead code paths and establishing a single-responsibility submit/publish/commit flow
- Resolved a queue-contention bottleneck by safely enabling multiple concurrent SQS consumers, eliminating event processing delays under load
- Fixed event-loop stall in async Excel conversion endpoint by offloading blocking file I/O to a threadpool, restoring API responsiveness under concurrent load
