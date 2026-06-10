---
type: project
slug: source-checker
name: Source Checker
employer: Farsight AI
role: sole author / service owner
period: 2026-03 — 2026-05
status: active
commits_by_kairi: 220
primary_languages: [Python, TypeScript]
technologies: [FastAPI, Temporal, SQLAlchemy, Alembic, PostgreSQL, AWS ECS, AWS CDK, S3, WorkOS, Azure OpenAI, Langfuse, Sentry, Docker, pre-commit, ruff, mypy]
domains: [claim verification, AI evaluation, document analysis, async workflow orchestration, clean architecture]
visibility: internal
---

# Source Checker

## What it is

A microservice that extracts factual claims from uploaded documents (PDFs, PowerPoints) and verifies each claim against evidence retrieved by a companion research-agent pipeline (RAP). Each document upload triggers a Temporal workflow that fans out claim extraction, evidence preparation, and per-claim verification as discrete activities, producing a structured verdict (`verified`, `rejected`, `uncertain`) with citations and explanations for every claim. The service exposes a REST API with WorkOS-authenticated upload and polling endpoints, a lightweight developer frontend, and a replay harness for offline evaluation against a hand-labeled golden set.

## My role & ownership

Sole author from initial scaffold commit through all 220 commits across the March–May 2026 window. Owned every layer: domain models, Temporal workflow and activities, REST API, CDK infrastructure, CI/CD pipeline, and the evaluation harness. The README lists Kairi as author.

## Key contributions

- **Service scaffold (DEV-622 / DEV-623 / DEV-624, PR #1–3, 2026-03-02 to 03-05):** Stood up the full clean-architecture skeleton — domain models, repositories, Unit-of-Work, Alembic migration, REST API endpoints, WorkOS JWT auth, and health check — from the first commit.
- **Temporal workflow foundation (2026-03-05 to 03-06):** Designed and implemented the `SourceCheckWorkflow` with full activity decomposition, retry policies, S3-backed inter-activity file passing (DEV-628), ECS worker service, and SSM-parameterized CDK deployment.
- **Prepare/verify pattern refactor (DEV-951 / DEV-952, PR #21–22, 2026-05-05 to 05-07):** Replaced the original single-pass fact-check flow with a two-phase prepare-then-verify design aligned to the RAP cross-service schema; introduced `prepare_evidence` and `verify_claim` activities, capped RAP fan-out concurrency to dampen burst load, and surfaced previously-swallowed `mark_claim_uncertain` failures.
- **Granular job status pipeline (DEV-974, PR #24, 2026-05-06 to 05-11):** Added phase-level status transitions across the extraction, preparation, and verification stages so callers and dashboards could track progress without polling job completion.
- **Golden-set fixture (DEV-1039, PR #25, 2026-05-12):** Built the evaluation fixture infrastructure — `GoldenFixture`/`Claim` Pydantic schema, `golden_claims_v1.json` with 63 claims from a real completed job, auto-labeled via deployed RAP (Claude Opus 4 + four external tools), zero hallucinations on numeric-token validation, and a fixture README documenting drain decomposition and regression guards.
- **Replay harness (DEV-1040, PR #26, 2026-05-12 to 05-14):** Implemented a CLI evaluation harness (`tests/harness/replay.py`) that runs the golden set against any verifier endpoint, emits per-claim JSONL with latency, agreement-with-golden, Langfuse trace IDs, and cost estimates, aggregates p50/p95 latency and uncertain-drain rates per configuration, and supports an N-sweep mode (`--n-turns 3,5`) for comparing research depth variants (DEV-1018 ship decision driver).
- **Langfuse trace propagation (DEV-1040 Stage 5, 2026-05-13):** Wired `langfuse_trace_id` through prepare and verify call chains so every evaluation run produces linkable LLM traces for cost and quality audit.

## Technologies & patterns

Clean architecture (domain / application / infrastructure / API layers) throughout. Temporal for durable async workflows with explicit retry policies and bounded concurrency. SQLAlchemy 2.x with Alembic migrations; domain enums typed directly onto ORM columns. FastAPI with WorkOS JWT middleware and chunked S3 upload. AWS CDK (TypeScript) for ECS Fargate workers, CodePipeline CI/CD, and SSM Parameter Store config hierarchy (`/{app}/{stage}/{param}`). Azure OpenAI for claim extraction and verification LLM calls. Langfuse for LLM observability and trace linking across evaluation runs. Pydantic v2 for strict schema validation on RAP cross-service payloads. `concurrent.futures.ThreadPoolExecutor` for in-harness fan-out (not asyncio, consistent with the sync activity pattern). ruff + mypy + pre-commit enforced in CI; `PLC0415` rule enforces top-of-file imports policy.

## Resume-ready bullets

- Built a claim-verification microservice from scratch (220 commits, sole author) that extracts factual claims from uploaded documents and verifies each against retrieved evidence using a two-phase Temporal workflow, producing `verified` / `rejected` / `uncertain` verdicts with citations.
- Designed and shipped a golden-set evaluation harness (DEV-1039 / DEV-1040) — 63 hand-labeled claims auto-labeled via Claude Opus 4 with zero numeric-hallucination failures — enabling data-driven ship decisions on verifier accuracy and uncertain-drain rate.
- Refactored the verification pipeline to a prepare/verify pattern (DEV-951 / DEV-952) aligned to the companion RAP service's schema, bounded fan-out concurrency to prevent burst overload, and surfaced previously-silent activity failures.
- Propagated Langfuse trace IDs through every evaluation run, producing per-claim cost, latency (p50/p95), and agreement-with-golden telemetry in a sweep-capable CLI harness for comparing research-depth configurations.
- Owned full-stack service delivery: clean-architecture Python backend, Temporal worker, CDK infrastructure (ECS Fargate, CodePipeline, SSM parameter hierarchy), WorkOS auth, and developer frontend — all from initial scaffold commit.
