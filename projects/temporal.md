---
type: project
slug: temporal
name: Temporal Standard & Shared Platform
employer: Farsight AI
role: author / infrastructure & platform lead
period: 2025-10 — 2026-06
status: active
commits_by_kairi: 98
primary_languages:
  - TypeScript
  - Python
technologies:
  - Temporal
  - AWS CDK (config-driven, TypeScript)
  - ECS Fargate
  - RDS PostgreSQL
  - AWS Secrets Manager
  - AWS KMS (customer-managed CMK)
  - AWS Parameter Store
  - CloudWatch
  - AWS ECR
  - CloudTrail
  - osv-scanner
  - GitHub Actions
  - Docker Compose
domains:
  - infrastructure
  - standards
  - orchestration
  - security
  - reliability / DR
visibility: internal
---

# Temporal Standard & Shared Platform

## What it is

The company's shared Temporal workflow platform and the authoritative internal standard governing how every Farsight service uses Temporal. Three connected artifacts:

1. **`TEMPORAL_STANDARD.md`** — a versioned, cross-repo specification (anchored at `~/Github/TEMPORAL_STANDARD.md`) defining SDK pinning, client singleton pattern, namespace strategy, retry policy categories (DB / LLM / API / File / Cache), heartbeating rules, large-payload offload via S3 Claim Check `PayloadCodec`, worker health checks, and graceful shutdown. Authored from scratch; the reference every Farsight Python service complies with.
2. **The shared platform** — the Temporal server, UI, and Postgres that every other Farsight service (monitor, source-checker, research-agent, email-service) runs its workflows on. A Docker Compose stack for local dev exposes a shared `edge` network so all other local service stacks attach with zero config; production runs on ECS Fargate + RDS.
3. **A config-driven CDK deployment + production-readiness posture** — migrated off the original SST implementation in June 2026 and hardened to production-grade security, reliability, and observability standards (below).

## My role & ownership

Sole author of the Temporal standard and primary owner of the shared platform. Initiated the repo, wrote the deployment infrastructure from initial commit, authored the onboarding and greenfield deploy/teardown runbooks, and — across June 2026 — drove the platform to production-readiness single-handed (24 merged PRs, DEV-1183 → DEV-1267).

## Key contributions

### The standard (2025-10 onward)
- Authored `TEMPORAL_STANDARD.md` — the company-wide spec: SDK version pinning, lazy singleton client with `asyncio.Lock`, namespace-per-service with auto-creation, per-category retry policies, heartbeat decision tree, S3 Claim Check payload offload, worker liveness probe, and SIGTERM graceful shutdown.
- Built and maintained the Docker Compose local dev stack with the shared `edge` network enabling zero-config attachment from every other service stack.
- Defined cross-service retry policy categories (DB / LLM / API / File / Cache) with explicit backoff parameters, replacing ad-hoc per-service retry logic.

### Production-readiness campaign (June 2026 — the shared platform hardening)
- **Migrated the stack from SST to config-driven CDK (DEV-1202, PR #5)** and removed the legacy SST database implementation (DEV-1187, PR #7), plus an IaC Temporal DB bootstrap deferred into the normal deploy path.
- **Brought all Temporal ECS services up to the ECS reliability standard R1–R6 (DEV-1203, PR #6)** — bind-IP health checks with a regression test, and reconciled reliability docs with the shipped behavior.
- **Least-privilege network posture:** restricted the Temporal frontend gRPC + UI ALB to internal sources (DEV-1183/1184, PR #8), tightened security-group egress to least-privilege (DEV-1240, PR #9), env-parameterized Web UI CORS to operator origins in prod (DEV-1185, PR #11), and fixed a UI→frontend egress regression on the NLB path (DEV-1260, PR #15).
- **Secrets & identity:** injected Temporal DB credentials from Secrets Manager and fixed the rotation Lambda's egress (DEV-1191, PR #10); split the shared ECS task role into per-service IAM roles (DEV-1225, PR #12); auto-restart Temporal server tasks on DB credential rotation, IAM-scoped to the four services and filtered on `AWSCURRENT` (DEV-1257, PR #18).
- **Encryption in transit & at rest:** enabled TLS to RDS via `rds.force_ssl=1` (DEV-1255, PR #17); customer-managed KMS CMK for prod RDS encryption (DEV-1190, PR #25).
- **Database resiliency & DR:** per-env deletion protection + a documented Multi-AZ / RTO / RPO / read-replica decision (DEV-1188, PR #13); a 35-day backup retention strategy with snapshot-on-delete and a restore runbook (DEV-1189, PR #14). Diagnosed that RDS Multi-AZ failover *wedges* the Temporal cluster (history can't reacquire shards) and that a task restart is the reliable recovery — then shipped automated log-alarm→restart failover recovery with a sandbox-drill-tuned wedge detector (DEV-1252, PR #23).
- **Supply chain & CI security gates:** mirrored all images to ECR and enabled scan-on-push (DEV-1223, PR #21); added a CI pipeline with PR security gates — secret scan, `osv-scanner` dependency scan, CDK build+test, actions pinned to SHAs, no persisted creds (DEV-1222, PR #19); remediated all critical + high dependency vulns (DEV-1267, PR #24); confirmed CloudTrail org-trail coverage (DEV-1226, PR #20).
- **Observability baseline:** 90-day log retention + CloudWatch alarms with synth assertions and mode-gated service-health alarms (DEV-1228, PR #22).
- **Adopted the shared-infra bastion standard**, dropping the dedicated bastion (DEV-1254, PR #16).
- Authored the **greenfield deploy/teardown runbook** and a **Security Production Readiness Worksheet**, correcting both to match deployed CDK reality rather than aspirational posture.

## Technologies & patterns

- **Temporal** — orchestration, namespace-per-service, `SandboxedWorkflowRunner`, class-based activities with constructor DI
- **Config-driven AWS CDK (TypeScript)** — single stack parameterized by account/stage; migrated off SST v3
- **Security** — Secrets Manager creds + rotation, per-service least-privilege IAM roles, least-privilege SG egress, KMS CMK at rest, TLS in transit, CORS restriction, ECR scan-on-push, `osv-scanner` CI gates, CloudTrail coverage
- **Reliability / DR** — ECS reliability standard R1–R6, per-env deletion protection, 35-day RDS backups + restore runbook, automated Multi-AZ-failover recovery
- **Claim Check pattern** — S3-backed `PayloadCodec` for oversized workflow payloads
- **Docker Compose** — multi-network local stack with shared `edge` bridge

## Resume-ready bullets

- Authored Farsight's company-wide Temporal standard (`TEMPORAL_STANDARD.md`) — SDK pinning, client singleton, namespace strategy, category-based retry policies, and S3 Claim-Check payload offload — adopted across all Python microservices.
- Drove the shared Temporal platform to production-readiness single-handed (24 PRs), migrating the stack from SST to config-driven CDK and hardening it across security, reliability, and observability standards.
- Implemented a full production security posture: Secrets Manager credential injection with rotation-triggered task restart, per-service least-privilege IAM roles, least-privilege security-group egress, customer-managed KMS CMK encryption at rest, TLS in transit, CI secret/dependency scanning gates, and ECR scan-on-push.
- Diagnosed that RDS Multi-AZ failover wedges the Temporal cluster (history shards can't reacquire) and shipped automated log-alarm-driven failover recovery, plus a 35-day backup strategy with a documented restore runbook and RTO/RPO decision.
- Designed a shared Docker `edge` network enabling zero-configuration Temporal attachment from every local service stack, eliminating per-engineer environment setup friction.
