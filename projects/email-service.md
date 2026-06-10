---
type: project
slug: email-service
name: Email Service
employer: Farsight AI
role: Owner / primary engineer
period: 2025-04 — 2026-06
status: active
commits_by_kairi: 102
primary_languages:
  - Python
  - TypeScript
technologies:
  - FastAPI
  - Celery
  - Temporal (temporalio >= 1.8)
  - Gmail API
  - Google Gemini (structured output)
  - python-pptx
  - AWS CDK (TypeScript)
  - AWS SSM Parameter Store
  - AWS ECS / ECR
  - AWS CodePipeline / CodeBuild
  - Docker / docker-compose
  - Pydantic v2
  - aiohttp
  - Poetry
domains:
  - AI-powered email automation
  - Async task processing
  - Workflow orchestration
  - Presentation generation
  - Cloud infrastructure
visibility: internal
---

# Email Service

## What it is

A Python microservice that monitors a Gmail inbox, classifies incoming requests with Gemini structured output, and executes predefined workflows — primarily generating multi-slide PowerPoint decks (buyer profiles, public market overviews, common stock comparisons) and replying with the files attached. Processing is asynchronous via Celery; in the latest phase the service was being migrated to Temporal for durable workflow orchestration.

## My role and ownership

Sole owner from the first commit. Built the service ground-up, authored all 102 commits across the full lifetime of the project — product logic, infrastructure, tests, and CI/CD pipeline.

## Key contributions

- **Built the service from scratch (2025-04):** Initial project structure, Gmail OAuth/service-account integration, workflow-parsing engine, Celery task queue, Docker/Compose setup, and Poetry dependency management.
- **Workflow classification engine:** Implemented introspection-based `WorkflowDefinition` builder that derives available workflows and their input schemas directly from service method docstrings, eliminating manual registration.
- **AI-powered routing:** Replaced rule-based email classification with Gemini structured output (`response_schema`) to identify workflow type and extract typed input fields; added fallback handling for partial or ambiguous inputs.
- **Slide aggregation pipeline:** Built PPTX-generation pipeline supporting buyer profiles, common-stock comparisons, and market overviews; added slide aggregator utility to merge outputs into a single deck and PPTX-to-PDF conversion for dual-format email replies.
- **Conversation context integration:** Added `get_thread_messages` to `GmailClient` (with HTML fallback) and injected thread history into AI prompts for context-aware responses.
- **CDK infrastructure (DEV-1103, 2026-05):** Authored a config-driven single-stack `EmailServiceBackendStack` in TypeScript CDK, replacing a three-stack split. Mirrors the rap-backup/research-agent pattern: ECS Fargate + ECR + CodePipeline + CodeBuild + SSM Parameter Store, with bootstrap-mode guard for first-deploy footgun avoidance.
- **Temporal migration (DEV-1097, DEV-1102, 2026-05):** Scaffolded `EmailWorkflow` and `InboxMonitorWorkflow` using the Temporal Python SDK; wired a real `poll_inbox` activity for inbox scraping; implemented cursor durability (history-ID tracking) and narrowed sandbox passthrough to address CodeRabbit review feedback.
- **SSM Parameter Store config (2026-01):** Migrated runtime configuration from environment files to SSM with hierarchical stage/shared paths retrieved via `env.py`, consistent with the team's Parameter Store pattern.

## Technologies and patterns

Celery + FastAPI for async task dispatch; Temporal Python SDK (`@workflow.defn` / `@activity.defn`) for durable inbox-scraping orchestration; Pydantic v2 models throughout; Gemini structured output for classification; python-pptx for slide generation; Gmail API via service-account impersonation; AWS CDK TypeScript for infra; SSM Parameter Store for config; Docker Compose for local development; Poetry for dependency management. Clean architecture layering: `src/email_service/`, `src/slide_generation/`, `src/temporal/`, `src/api/`.

## Resume-ready bullets

- Owned and built a Python email-automation microservice end-to-end, from initial project scaffolding through production CDK infrastructure, serving AI-assisted slide-generation workflows for M&A deal teams.
- Designed a docstring-introspection engine that automatically derived Pydantic-typed workflow definitions from service methods, eliminating manual schema registration and keeping input contracts self-documenting.
- Replaced rule-based email classification with Gemini structured output routing, enabling reliable extraction of typed workflow inputs from free-form analyst emails.
- Migrated async email processing from Celery to Temporal (Python SDK), implementing `InboxMonitorWorkflow` with durable cursor tracking to survive worker restarts without re-processing messages.
- Authored a config-driven AWS CDK stack (TypeScript) consolidating ECS Fargate, ECR, CodePipeline, CodeBuild, and SSM Parameter Store into a single synth, replacing a three-stack manual deployment.
---
