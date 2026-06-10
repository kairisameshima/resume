---
type: project
slug: farsight-shared-services
name: Farsight Shared Services
employer: Farsight AI
role: primary contributor and co-owner
period: 2025-03 — 2025-09
status: maintained
commits_by_kairi: 131
primary_languages: [Python]
technologies: [Pydantic v2, OpenAI SDK, Google GenAI SDK, Vertex AI, Pinecone, boto3, S3, httpx, pytest, Black, flake8, mypy, AWS CodeArtifact]
domains: [shared library, AI model wrappers, document storage, vector database, third-party API integration]
visibility: internal
---

# Farsight Shared Services

## What it is
A Python package (`farsight-shared-services`, published to AWS CodeArtifact) that provides reusable abstractions shared across Farsight's microservices. It organized common concerns — AI model clients (Gemini, OpenAI, GenAI Gemini), document storage (S3), vector database (Pinecone), dataroom processing, and third-party API wrappers (Crunchbase, Capital IQ) — behind typed interfaces and Pydantic models so downstream services consumed a consistent, versioned contract rather than raw SDK calls.

## My role & ownership
Primary contributor (131 Kairi-authored commits) responsible for the majority of the library's domain surface. Drove the AI model wrapper layer, the document storage abstractions, the relay-URL configuration pattern, and all third-party API integrations added through mid-2025.

## Key contributions
- Bootstrapped the Gemini/Vertex AI model wrapper (`GeminiVision`, `GenaiGeminiChat`) with structured-response support via Pydantic models, configurable `GenerateContentConfig`, and token-usage logging with caller-context attribution (2025-03 – 2025-05)
- Added `chat_with_schema` to `GenaiGeminiChat` enabling structured outputs backed by Pydantic response schemas, used across multiple downstream services
- Extended `S3DocumentStorage` with dataroom-scoped key prefixing, presigned URL retrieval, dataroom/source-file listing, and a `DocumentLocation` model; added corresponding unit tests using `moto` for hermetic S3 mocking
- Built the Crunchbase API integration (DEV-62): typed interface + Pydantic models + concrete implementation with robust data-handling and type-safety refactors
- Built the Capital IQ (CapIQ) API integration (DEV-80): data models, structured response handling, simplified processing logic
- Implemented the OpenAI API wrapper with relay URL support and timeout configuration (DEV-62/DEV-82), centralizing all relay URL config so every consuming service resolved the endpoint from a single source
- Added synchronous embedding methods to AI model classes, expanding the interface beyond chat-only use cases
- Bumped the package through multiple versions (0.1.1 → 0.1.16) with backward-compatible interface evolution, maintaining a stable import surface for downstream consumers
- Added `async_retry` decorator for async error handling and introduced `GENERIC_QUESTIONS` to `ContextType` enum for vector DB context generation

## Technologies & patterns
Interface-first design: every domain (AI models, document storage, vector DB, third-party APIs) defined an abstract `Interface` class with concrete implementations behind it. Pydantic v2 for all domain models. `moto` for S3 unit tests. `google-genai` SDK with `GenerateContentConfig` for structured outputs. `pinecone` for vector upsert with namespace support. Package distributed via AWS CodeArtifact with Poetry; versioning via `pyproject.toml` bumps. Black + flake8 + mypy for code quality.

## Resume-ready bullets
- Built and maintained the shared Python library consumed by all Farsight microservices, covering AI model wrappers (Gemini, OpenAI), document storage (S3), vector database (Pinecone), and third-party data APIs (Crunchbase, Capital IQ)
- Designed an interface-first abstraction layer that decoupled downstream services from raw SDK calls, enabling provider swaps and API evolution without breaking consumers
- Centralized LLM relay URL configuration into a single wrapper (DEV-82/DEV-62), eliminating per-service hardcoded endpoint logic across the codebase
- Integrated Crunchbase and Capital IQ APIs with typed Pydantic models and structured response handling, providing the data-sourcing foundation for Farsight's research pipeline
- Extended S3DocumentStorage with dataroom-scoped key prefixing, presigned URL generation, and `moto`-backed unit tests, enabling reliable document retrieval across isolated tenant namespaces
