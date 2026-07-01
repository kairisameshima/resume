---
type: project
slug: retrieval-agent-playground
name: Retrieval Agent (Research Agent)
employer: Farsight AI
role: primary owner
period: 2025-09 — 2026-07
status: active
commits_by_kairi: 429
primary_languages: [Python, TypeScript]
technologies: [FastAPI, Temporal, AWS Bedrock, Claude (Sonnet 4 / Opus 4), AWS CDK, ECS Fargate, ElastiCache Redis, DynamoDB, AWS Secrets Manager, AWS KMS, AWS WAF, CloudWatch, AWS ECR, Cohere, SEC EDGAR, Exa, S3, Sentry, Langfuse, auditry/structlog, Poetry, ruff, ty (astral), trivy, GitHub Actions]
domains: [agentic-retrieval, financial-research, LLM-orchestration, streaming-APIs, observability, eval-infra, cloud-infra, security, reliability / DR]
visibility: internal
---

# Retrieval Agent (Research Agent)

## What it is

An agentic research service that answers financial and open-domain questions by autonomously orchestrating multi-turn tool calls across SEC EDGAR, S&P Capital IQ, Exa web search, and PDF corpora, then streams cited responses over SSE. The backend is built on FastAPI + Temporal and deployed on AWS ECS Fargate with per-environment CDK stacks.

## My role & ownership

Primary owner from the ground up. Drove the full arc: initial Lambda-based prototype → Temporal workflow migration → production multi-environment deployment (dev / playground / prod). Owned all architectural decisions, infra CDK, CI/CD pipelines, performance profiling, eval infrastructure, and developer tooling.

## Key contributions

- **Temporal migration (DEV-759 → DEV-762, PR #112–#113):** Converted all async integration chains (EDGAR, CIQ, Exa, PDF, GFD, PitchBook) from async to sync, implemented `ResearchWorkflow` in Temporal with S3 payload offload for large tool results, separate task queues (DEV-763), and per-tool retry policies (DEV-764).
- **Multi-environment CDK consolidation (DEV-929, PR #131):** Collapsed per-environment CDK stacks into a single config-driven stack with per-deployment SSM namespaces, ElastiCache Redis for session event streams, ECS Service Connect, and a bootstrap mode for first-deploy of new envs.
- **Production stand-up (DEV-1082, PR #154):** Scaffolded `ResearchAgentBackend-prod` with Redis auth token wiring, Temporal-prod NLB DNS, manual approval gate between Build and Deploy, and `bootstrap-prod-ssm.sh` hardening.
- **CI/CD deploy workflows (DEV-1093/DEV-1094, PR #155–#156):** Added GitHub Actions deploy workflows for dev / playground / prod, Slack deploy notifications, and diff-scoped lint/format/type/test gates on PRs.
- **Agentic verifier with retrieval loops (DEV-1018, PR #150):** Built a two-stage claim verifier: Stage A surfaces per-call token telemetry in `VerifyClaimOutput`; Stage B adds an N-turn retrieval deepening loop with Redis-cached EDGAR filings (DEV-1015) and worker heartbeat/cancel bounding (DEV-1016).
- **Performance profiling and load-test harness (DEV-909, PR #117):** Instrumented per-activity Schedule→Start timings via Temporal history, built a load-test UI with fire-pattern controls and CSV export, split the worker into workflows + activities containers via `WORKER_ROLE` env var, tuned connection pools, and resolved a concurrency cliff that surfaced under parallel SEC filing load.
- **Streaming wedge diagnosis and fix (DEV-1145, PR #168):** Identified an event-loop wedge on `StreamingResponse` paths caused by `auditry >=0.2.7`; pinned to 0.2.6, instrumented the loop for future capture, and documented the root cause.
- **Type-checking ratchet and CI hardening (DEV-866, DEV-1156, PR #113 / #173):** Aligned Temporal implementation with Farsight standard, replaced mypy with `ty` (astral), burned down 7 single-digit ty rules to error severity, and flipped lint/format gates to whole-tree.
- **Eval infrastructure:** Built DynamoDB-backed test-case management with versioned ratings, per-backend-version pass-rate analysis (`/eval/analysis/summary`), and snapshot capture for regression tracking.
- **`/debug/config` endpoint and SSM consolidation (DEV-1105 / DEV-979):** Added runtime config inspection endpoint; consolidated SSM params into a config-driven CDK naming scheme and renamed `DEVELOPER` → `DEPLOYMENT_SLUG`.

### Production-readiness campaign (June–July 2026, DEV-1297 → DEV-1362)
Drove the research-agent's own prod-readiness gauntlet — the security, reliability, and supply-chain hardening required before the service can go to production (tracked as a 37-ticket Linear project across Standards / Resiliency / Research-Agent-Specific milestones).
- **Security posture:** customer-managed KMS CMK with rotation for data-at-rest (DEV-1297); moved vendor API credentials to Secrets Manager with a rotation runbook (DEV-1301/DEV-1302); scoped task-role and execution-role IAM to least privilege, region sourced from config (DEV-1303/DEV-1317); WAF on the playground public ALB (DEV-1318); CORS restricted to an explicit allow-list, no wildcard (DEV-1314); locked down worker egress (DEV-1305); made ECS Exec break-glass rather than standing prod access (DEV-1316); disabled Sentry `send_default_pii` and added a before-send scrubber that redacts non-string sensitive values (DEV-1298).
- **Supply chain & CI gates:** SCA + SBOM and secret-detection scan gates in CI via trivy (DEV-1299/DEV-1300), remediation of HIGH dependency vulnerabilities (DEV-1362), ECR scan-on-push with an untagged-image lifecycle (DEV-1331), prod promotion gated on tests + scan reports (DEV-1309), and deploy-by-image-digest instead of `:latest` (DEV-1310).
- **Reliability / DR:** Redis Multi-AZ with automatic failover for prod (DEV-1324), ECS deployment circuit breaker with rollback (DEV-1325), target-tracking autoscaling for API + worker services (DEV-1327), ≥2 API tasks in prod with container health checks (DEV-1328/DEV-1329), and explicit ALB deregistration delay (DEV-1333).
- **Observability:** prod CloudWatch saturation alarms + SNS on-call topic (DEV-1311), 90-day ECS log-group retention (DEV-1312).
- **Networking (DEV-1343):** joined the research-agent to the Service Connect mesh for cross-service OAuth access.
- **Trace hygiene (DEV-1258):** upserted Langfuse traces so workflow runs are no longer orphaned in observability.

## Technologies & patterns

- **Orchestration:** Temporal Python SDK — deterministic workflow fan-out with `asyncio.gather`, heartbeated long activities, per-role worker split (workflows vs activities containers)
- **API:** FastAPI with SSE streaming (`/chat/stream`), sync (`/chat/sync`), eval, and debug endpoints; Pydantic v2 for all I/O models
- **LLMs:** AWS Bedrock inference profiles — Claude Sonnet 4 (quick mode, 8 K tokens) and Claude Opus 4 (deep mode, 15 K + 5 K thinking budget); automatic 1 M extended context at >160 K tokens
- **Integrations:** SEC EDGAR (edgartools + sec-api Elasticsearch fallback), S&P Capital IQ, Exa web search, PitchBook, GFD, PDF pipeline
- **Citations:** Character-level citation tracking with div-ID anchoring for EDGAR HTML filings and page-number anchoring for PDFs
- **Infra:** AWS CDK (TypeScript) for ECS Fargate, ElastiCache Redis, SSM Parameter Store, S3, DynamoDB, CodePipeline; multi-env config-driven stack pattern
- **Observability:** structlog + auditry, Sentry on both FastAPI and Temporal workers, Langfuse trace threading
- **Tooling:** ruff, ty (astral), pre-commit hooks, Poetry, GitHub Actions diff-scoped CI

## Resume-ready bullets

- Architected and owned Farsight's agentic research service end-to-end — migrated from Lambda prototype to Temporal-orchestrated multi-worker pipeline and stood up production on ECS Fargate across dev, playground, and prod environments.
- Built a two-stage claim verifier with N-turn retrieval deepening, Redis-cached EDGAR filings, and per-call token telemetry, enabling automated source verification at scale.
- Designed a character-level citation system with div-ID anchoring for SEC EDGAR filings and sentence-level precision for web sources, surfaced as a streaming SSE API consumed by the product frontend.
- Instrumented per-activity Schedule→Start latency via Temporal history, diagnosed a concurrency cliff under parallel filing load, and tuned worker pools to resolve it — guided by a custom load-test UI with fire-pattern controls.
- Established CI quality gates (diff-scoped lint/format/type checks, type-checking ratchet with ty, Slack deploy notifications) and a DynamoDB-backed eval framework for tracking response quality across backend versions.
- Drove the service's production-readiness campaign (DEV-1297 → DEV-1362): KMS CMK encryption, Secrets Manager vendor-credential rotation, least-privilege IAM, WAF, CORS allow-listing, SCA/SBOM/secret CI scan gates, deploy-by-digest with test-gated promotion, Redis Multi-AZ failover, ECS circuit-breaker rollback, autoscaling, and CloudWatch on-call alarming.
