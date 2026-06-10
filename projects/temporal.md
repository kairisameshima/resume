---
type: project
slug: temporal
name: Temporal Standard & Infrastructure
employer: Farsight AI
role: author / infrastructure lead
period: 2025-10 — 2026-06
status: active
commits_by_kairi: 12
primary_languages:
  - TypeScript
  - Python
technologies:
  - Temporal
  - AWS SST v3
  - ECS Fargate
  - RDS PostgreSQL
  - Docker Compose
  - AWS Parameter Store
domains:
  - infrastructure
  - standards
  - orchestration
visibility: internal
---

## What it is

The company's shared Temporal workflow infrastructure and the authoritative internal standard governing how every Farsight service uses Temporal. Two distinct but connected artifacts:

1. **Deployment repo** — Docker Compose stack (Temporal server, UI, Postgres) for local development plus AWS SST v3 configuration for ECS Fargate + RDS production deployment. Exposes a shared `edge` Docker network so all other local service stacks attach without additional configuration.
2. **TEMPORAL_STANDARD.md** — a versioned, cross-repo specification (anchored at `/Users/kairisameshima/Github/TEMPORAL_STANDARD.md`) that defines SDK pinning, client singleton pattern, namespace strategy, retry policy categories (DB / LLM / API / File / Cache), heartbeating rules, large-payload offload via S3 Claim Check `PayloadCodec`, worker health checks, and graceful shutdown. The standard was authored from scratch and is the reference every Farsight Python service is expected to comply with.

Also ships: a production Security Readiness Worksheet (`docs/SECURITY_PRODUCTION_READINESS.md`) authored to capture the deployment's security posture against CDK-deployed infrastructure reality.

## My role & ownership

Sole author of the Temporal standard document and primary infrastructure contributor. Initiated the repo, wrote the deployment infrastructure from initial commit through operational maturity, authored the onboarding guide, and drafted the security readiness worksheet.

## Key contributions

- Authored `TEMPORAL_STANDARD.md` — the company-wide specification for Temporal SDK usage covering SDK version pinning, lazy singleton client with `asyncio.Lock`, namespace-per-service strategy with auto-creation, per-category retry policies with explicit retry parameters, heartbeat decision tree, S3 Claim Check payload offload pattern, worker liveness probe (EvenUp pattern), and SIGTERM graceful shutdown.
- Built and maintained the Docker Compose local dev stack, including shared `edge` network design enabling zero-config attachment from all other service stacks.
- Added SST v3 AWS deployment configuration targeting ECS Fargate + RDS PostgreSQL.
- Wrote the Temporal onboarding guide (`docs/`) to reduce ramp time for engineers joining the stack.
- Authored the security production readiness worksheet correcting it to match deployed CDK infrastructure reality, not aspirational posture.
- Aligned the local Docker Compose configuration with the running production stack.

## Technologies & patterns

- **Temporal** — workflow orchestration, namespace-per-service, `SandboxedWorkflowRunner`, class-based activities with constructor DI
- **AWS SST v3** — infrastructure-as-code for ECS / RDS production deployment
- **Docker Compose** — multi-network local stack with shared `edge` bridge
- **AWS Parameter Store** — `TEMPORAL_HOST` env → SSM fallback URL resolution pattern
- **Claim Check pattern** — S3-backed `PayloadCodec` for oversized workflow payloads
- **Category-based retry policies** — typed retry configurations (DB / LLM / API) shared across services

## Resume-ready bullets

- Authored and shipped the company-wide Temporal standard (`TEMPORAL_STANDARD.md`), covering SDK pinning, client singleton pattern, namespace strategy, category-based retry policies, and S3 Claim Check payload offload — adopted across all Farsight Python microservices.
- Designed a shared Docker `edge` network architecture enabling zero-configuration Temporal attachment from every local service stack, eliminating per-engineer environment setup friction.
- Delivered Temporal production infrastructure on AWS using SST v3 (ECS Fargate + RDS PostgreSQL), and authored the accompanying onboarding guide and security production readiness worksheet.
- Defined cross-service retry policy categories (DB / LLM / API / File / Cache) with explicit backoff parameters, replacing ad-hoc per-service retry logic with a single referenced standard.
