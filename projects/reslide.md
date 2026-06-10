---
type: project
slug: ReSlide
name: ReSlide
employer: Farsight AI
role: primary owner / sole backend engineer
period: 2025-10 — 2026-02
status: maintained
commits_by_kairi: 263
primary_languages: [Python]
technologies: [FastAPI, Temporal, SQLAlchemy, Alembic, PostgreSQL, S3, AWS ECS Fargate, AWS CDK, SST, Docker, Pydantic, OpenAI, Google Gemini, Pinecone, python-pptx, Celery, auditry]
domains: [workflow orchestration, document generation, AI-assisted research, slide replication, microservice architecture]
visibility: internal
---

# ReSlide

## What it is

ReSlide is an AI-powered PowerPoint generation service that replicates slide decks with brand-new data. Given a template slide deck and a business context, the service runs a multi-stage pipeline: it ingests and dissects the template, conducts AI-driven research on relevant topics, produces a research report for human review, and then generates a fully populated slide deck using the original template's visual structure. The system is built on Temporal for durable workflow orchestration and deployed on AWS ECS Fargate.

## My role & ownership

Primary and effectively sole backend engineer from initial commit (2025-10-07) through the end of the tracked period (2026-02-23). Owned every layer: domain models, repository layer, Temporal workflow and activity design, FastAPI API surface, Alembic migrations, Dockerization, and AWS CDK/SST infrastructure-as-code. Also drove the multi-milestone feature roadmap (M1–M4) that restructured the service from a single monolithic workflow into a staged, human-in-the-loop generation pipeline.

## Key contributions

- **Initial service build-out (2025-10, PRs #1–#4):** Bootstrapped the entire FastAPI + PostgreSQL + Temporal + Docker stack from scratch, including CDK infrastructure, SSM Parameter Store configuration, and the first working slide generation workflow.
- **Multi-slide support (DEV-112, PRs #10–#12, 2025-11):** Designed and implemented multi-component template ingestion and generation in five phased commits — new domain models, PPTX XML/ZIP processing utilities, Temporal activities, parallel `asyncio.gather`-based execution, and the API layer.
- **M1 — Workflow architecture refactor (PRs #40–#45, 2026-02):** Decomposed a monolithic `SlideGenerationWorkflow` into separate `ResearchGenerationWorkflow`, `SlideGenerationWorkflow`, and `OrchestratorWorkflow`, separating research and generation concerns, migrating enums to `StrEnum`, and fixing Temporal replay-determinism bugs.
- **M2/M3 — Research plan and report review-approval gates (PRs #46–#56, DEV-492–DEV-521, 2026-02):** Built the full human-in-the-loop approval layer — GET/PUT/approve endpoints for research plans and research reports, `AWAITING_APPROVAL` status transitions, S3-backed report storage with try/except upload safety, and `MissingResearchError` fast-fail on missing pre-computed research.
- **M4 — Generation setup and DEV-565 bypass (PRs #61–#63, 2026-02):** Added the generation-setup milestone workflow and an `auto_generate` bypass flag wired through the Temporal `OrchestratorWorkflow` input for non-gated execution paths.
- **Logo replacement pipeline fixes (DEV-453, PR #36, 2026-02):** Eliminated duplicate Crunchbase API calls and added per-logo try/except with exception chaining for S3 upload/download in the logo pass-2 pipeline.
- **Observability integration (DEV-65, PR #6, 2025-11):** Replaced `print` statements service-wide with structured `auditry` logger calls.

## Technologies & patterns

- **Temporal** for durable, replay-safe workflow orchestration across three workflow types (template ingestion, research generation, slide generation) coordinated by an `OrchestratorWorkflow`. Activities are sync `def` running on a `ThreadPoolExecutor` for blocking I/O; fan-out uses `asyncio.gather` for replay determinism.
- **Clean architecture** with strict layer separation: `domain/` holds models, constants, and exceptions; `application/temporal/` holds workflows and activities; `infrastructure/` holds SQLAlchemy repositories and persistence; `api/` exposes FastAPI routes backed by use-case services.
- **Human-in-the-loop approval gates** at both research-plan and research-report stages, with `AWAITING_APPROVAL` status and explicit approve/edit endpoints before generation proceeds.
- **AWS ECS Fargate + CDK + SSM Parameter Store** for infrastructure, following the project-wide stage-scoped parameter pattern (`/reslide/{stage}/{param}`).
- **python-pptx + lxml + xmltodict** for PPTX XML/ZIP manipulation in the replication engine.

## Resume-ready bullets

- Built and owned an AI-powered slide-generation service end-to-end in Python, orchestrating multi-stage research and generation pipelines with Temporal across three workflow types on AWS ECS Fargate.
- Designed and shipped a human-in-the-loop approval architecture (research plan → research report → slide generation) with explicit review/edit/approve REST endpoints and durable `AWAITING_APPROVAL` status transitions.
- Refactored a monolithic Temporal workflow into a composed `OrchestratorWorkflow` coordinating `ResearchGenerationWorkflow` and `SlideGenerationWorkflow`, fixing replay-determinism bugs and separating research and generation concerns.
- Implemented parallelized multi-component PPTX template ingestion and slide generation using `asyncio.gather`, cutting per-deck processing time for multi-slide templates.
- Bootstrapped the full service stack from initial commit — FastAPI, PostgreSQL, Alembic, Docker, AWS CDK infrastructure-as-code, and SSM-backed configuration — across 263 commits over four months.
