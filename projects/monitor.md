---
type: project
slug: monitor
name: Monitor
employer: Farsight AI
role: Owner / primary backend engineer
period: 2025-05 — 2026-05
status: active
commits_by_kairi: 477
primary_languages: [Python]
technologies: [FastAPI, Temporal, SQLAlchemy, PostgreSQL, Redis, Alembic, Docker, AWS ECS, AWS CDK, AWS Parameter Store, AWS CodePipeline, Sentry, Prometheus, Google Gemini, SEC API, FMP API, WorkOS, asyncpg, Pydantic]
domains: [AI-driven monitoring, financial data pipelines, workflow orchestration, clean architecture]
visibility: internal
---

# Monitor

## What it is
An AI-powered monitoring service that runs configurable, scheduled queries against financial and web data sources (SEC filings, earnings transcripts, web search), uses Google Gemini to evaluate and deduplicate stories, and dispatches email notifications to users when material events are detected.


## My role & ownership
Owner and primary backend engineer. Built the service from initial commit through full production deployment, including the initial Celery-based architecture and a complete migration to Temporal, plus all CDK infrastructure.

## Key contributions
- Architected and shipped the initial service (2025-05): FastAPI + Celery + PostgreSQL stack with a modular reporter pipeline (web search, SEC filings, earnings transcripts).
- Designed and implemented the SEC filings sub-system: real-time WebSocket ingestion from SEC API, vector embedding + descriptor generation, deduplication via TOCTOU-safe content-hash insertion (DEV-422), and semantic similarity scoring (DEV-386).
- Led the full Celery → Temporal migration across five milestones (PRs #100–#105, DEV-349–DEV-351, DEV-357, DEV-376–DEV-379): replaced all Celery tasks with granular Temporal activities covering the orchestrator, reporter, evaluator, notifier, and SEC filing workflows.
- Implemented the Unit of Work pattern for Temporal activities (DEV-411–DEV-414, PRs #141–#144): introduced async SQLAlchemy repositories, async domain services, and constructor-injected activity classes so each activity owns its session lifecycle without leaking connections.
- Drove an async SQLAlchemy infrastructure buildout (DEV-371–DEV-379, PRs #121–#129): async engine, asyncpg driver, complete set of async repository interfaces, and worker reconfiguration to match.
- Added Redis payload cache for reporter activities (PR #113) and doubled worker concurrency to reduce activity backlog, then converted the full stack back to sync Temporal activities (DEV-412–DEV-414) after profiling revealed blocking I/O mismatch.
- Built a Temporal worker liveness healthcheck driven by SDK Prometheus metrics (DEV-934, PR #154–#155): tracks in-flight activity slots and stale-worker thresholds; integrated Sentry exception reporting for worker-level failures.
- Set up CDK infra stacks (monitor-infra-stack, monitor-param-stack) with ECS, RDS, CodePipeline, and Parameter Store, and added a force-push dev-deployment pipeline (DEV-940, PR #156).
- Established unit test suite with pytest (DEV-397, DEV-399, PRs #135–#136) and enforced top-of-file imports, naming conventions, and activity modularization across the codebase (DEV-388–DEV-390, PRs #132–#134).

## Technologies & patterns
- **Languages/frameworks:** Python 3.12, FastAPI, SQLAlchemy 2.0 (sync + async), Alembic, Pydantic v2, asyncpg
- **Workflow orchestration:** Temporal (activities, child workflows, retry policies, SDK metrics-based liveness)
- **Data / AI:** Google Gemini (story evaluation, schema generation), SEC API + WebSocket, FMP API (earnings transcripts), Redis (payload cache), PostgreSQL (vector embeddings + semantic deduplication)
- **Infrastructure:** AWS ECS (Fargate), AWS CDK (TypeScript), AWS CodePipeline, AWS Parameter Store, AWS RDS, Sentry, Prometheus
- **Auth:** WorkOS, PyJWT
- **Patterns:** Clean Architecture (domain / application / infrastructure), Unit of Work, Repository, constructor-based DI for Temporal activities, class-based Temporal activities

## Resume-ready bullets
- Migrated a production AI monitoring service from Celery to Temporal across five parallel milestones, refactoring all worker activity code into granular, class-based Temporal activities with constructor dependency injection and a Unit of Work pattern for session management.
- Designed and shipped an SEC filings ingestion pipeline with real-time WebSocket intake, vector embeddings, semantic deduplication, and TOCTOU-safe concurrent insertion, integrated as a first-class Temporal workflow.
- Built a Temporal worker liveness healthcheck using SDK-emitted Prometheus metrics to track in-flight activity slots and stale-worker thresholds, eliminating blind spots in ECS health monitoring.
- Established async SQLAlchemy infrastructure (asyncpg, async repositories, async domain services) and later profiled and reverted to sync activities, improving worker throughput and eliminating redundant async-wrapping overhead.
- Authored CDK infrastructure stacks (ECS, RDS, CodePipeline, Parameter Store) and a force-push dev-deployment pipeline, enabling one-command environment promotion across dev and staging.
