---
type: project
slug: api-relay
name: API Relay (LLM Relay)
employer: Farsight AI
role: sole owner and primary engineer
period: 2025-07 — 2026-05
status: active
commits_by_kairi: 158
primary_languages: [Python]
technologies: [FastAPI, Uvicorn, Redis, arq, Pydantic v2, OpenAI SDK, Google GenAI SDK, Anthropic Bedrock SDK, Temporal, AWS CDK, GCP Vertex AI, Docker, Ruff, pytest, pytest-cov, Prometheus]
domains: [LLM gateway, async job processing, streaming, multi-provider AI, infrastructure]
visibility: internal
---

# API Relay (LLM Relay)

## What it is
A production FastAPI microservice that acts as a centralized relay for all LLM calls across Farsight's platform. It unified access to Azure OpenAI, Google Gemini (Vertex AI), and Anthropic Bedrock behind a single consistent API surface, with support for structured outputs, document processing, function calling, async job queuing via Redis/arq, and real-time streaming via Redis Streams SSE. The service handled sync-to-async fallback on timeout, schema validation, Google Search grounding, and correlation tracking.

## My role & ownership
Sole owner and primary engineer from initial commit through production. Designed the architecture, built every major subsystem, led the AWS-to-GCP cloud migration, and drove the test coverage campaign that brought the suite to 99% (665 tests) with hermetic isolation.

## Key contributions
- Built the original FastAPI service from scratch (2025-10): structured outputs with schema validation, sync-to-async fallback, Azure OpenAI and Gemini adapters, correlation ID middleware, Redis/arq async job queue
- Added document and image processing capabilities with a provider-agnostic interface covering PDFs and images across both OpenAI and Gemini providers
- Implemented Anthropic Bedrock adapter and function calling endpoints for both OpenAI and Gemini, enabling tool-use workflows across all three providers
- Added Google Search grounding, temperature/max-tokens controls, and a `/relay/gemini/full` endpoint returning raw `GenerateContentResponse` for downstream flexibility
- Led AWS-to-GCP cloud migration (2026-03): switched auth to Application Default Credentials, consolidated on Vertex AI, removed AWS-specific infra (Sentry/GCS bucket/auditry), updated Parameter Manager path handling
- Built Gemini streaming endpoint with guard against double-enqueue after partial stream content; added Redis AUTH string support for GCP Memorystore
- Added Temporal workflows and Redis Streams SSE streaming as Phase 1 of a queue-backed streaming architecture
- Drove scalability improvements (DEV-697): fixed QueueClient memory leak, added uvicorn worker recycling with jitter (`--limit-max-requests-jitter`), tuned auto-scaling and health check tolerances under sustained load
- Added WAF rule to block raw ALB IP requests (infra hardening)
- Executed a comprehensive test overhaul (2026-05): wrote 29 test files, enforced hermetic isolation (patched all external I/O), reached 99% coverage / 665 tests with zero warnings

## Technologies & patterns
FastAPI with layered clean architecture (`api/`, `services/`, `workers/`, `domain/`). Pydantic v2 for all request/response models and schema validation. Redis for async job state and streaming. arq for background task workers. Multi-provider LLM adapter pattern (OpenAI, Gemini, Anthropic Bedrock) behind a unified completion service. Ruff for linting/formatting. pytest with `pytest-asyncio`, `pytest-mock`, `pytest-cov`; hermetic isolation via `SimpleNamespace` dependency patching. AWS CDK for infra-as-code across dev/staging/prod. GCP Vertex AI + ADC auth post-migration.

## Resume-ready bullets
- Owned and built an LLM relay service from the ground up in FastAPI, unifying Azure OpenAI, Google Gemini, and Anthropic Bedrock behind a single API with async job queuing, Redis Streams SSE, and structured-output schema validation
- Led a full AWS-to-GCP cloud migration, switching authentication to Application Default Credentials and consolidating the service on Vertex AI with zero downtime
- Drove a test coverage campaign that reached 99% coverage across 665 hermetically isolated unit tests, eliminating all external I/O dependencies in the test suite
- Resolved a QueueClient memory leak and added uvicorn worker recycling with jitter under sustained load, improving stability during high-concurrency conditions
- Designed and shipped function calling endpoints for both OpenAI and Gemini, enabling tool-use workflows across Farsight's research and data-extraction pipeline
