---
type: project
slug: cosmos
name: Cosmos (Service Scaffold Template)
employer: Farsight AI
role: author / maintainer
period: 2025-12 — 2026-04
status: active
commits_by_kairi: 18
primary_languages:
  - Python
  - TypeScript
  - Jinja2
technologies:
  - Cookiecutter
  - FastAPI
  - Temporal
  - MinIO
  - Docker Compose
  - AWS CDK
  - ECS Fargate
  - RDS
  - EventBridge
  - Ruff
  - pre-commit
domains:
  - tooling
  - scaffolding
  - infrastructure
visibility: internal
---

## What it is

Cosmos is the internal Cookiecutter-based service scaffold template for Farsight. Engineers run a single `cookiecutter` invocation and answer a prompt sequence to generate a fully operational microservice skeleton: FastAPI application, optional RDS database (async or sync SQLAlchemy), optional JWT auth, optional EventBridge integration, optional Temporal worker, CI/CD pipeline, CDK infrastructure stack, and local dev environment via Docker Compose — all wired together and ready to run.

The template uses Jinja2 (`.j2`) conditional blocks throughout to compose only the features selected during generation, and a `post_gen_project.py` hook prunes directories and files that don't apply to the chosen configuration.

## My role & ownership

Primary author and maintainer. Drove the template from initial scaffolding through the addition of the Temporal worker feature set, which was the largest single contribution (1,376 lines across 30 files in one PR).

## Key contributions

- Built the optional Temporal worker feature (`feat/temporal-worker` → PR #7): added `src/temporal/` module with lazy singleton client, class-based activity shell, Claim Check S3 codec, retry policy catalog, heartbeat utilities, health probe, graceful shutdown handler, and worker entrypoint — all compliant with `TEMPORAL_STANDARD.md`.
- Bundled MinIO as the local S3-compatible store so the Claim Check pattern works in Docker Compose without an AWS account.
- Implemented env-var fallback logic in `src/env.py.j2` to degrade gracefully from SSM Parameter Store to local env vars, enabling local dev without live AWS.
- Added conditional `sst/` directory pruning: when `with_eventbridge=no`, the SST infrastructure directory is dropped at generation time to avoid dead code.
- Replaced Black with Ruff for formatting and added pre-commit hooks with reproducible `rev` pinning; bumped Ruff to v0.15.4 and corrected the post-gen error messaging.
- Added the `Annotated CurrentUser` dependency-injection type alias pattern to the auth module, reducing boilerplate in route signatures across generated services.
- Aligned generated CDK stacks with the secure-parameters pattern (SSM-backed secrets, no plaintext in task definitions).

## Technologies & patterns

- **Cookiecutter + Jinja2** — conditional template rendering with `post_gen_project.py` pruning hook
- **Temporal Claim Check** — `PayloadCodec` offloading large workflow payloads to MinIO (local) / S3 (AWS)
- **MinIO** — local S3-compatible storage bundled in Docker Compose for offline Claim Check testing
- **Env-var fallback** — `TEMPORAL_HOST` env → SSM Parameter Store → `localhost:7233` resolution chain
- **CDK** — generated infrastructure stacks for ECS / RDS / EventBridge with Parameter Store integration
- **Ruff** — linting + formatting enforced via pre-commit across all generated services

## Resume-ready bullets

- Built the Temporal worker module for Cosmos, the company's internal service scaffold — a 1,376-line contribution adding a standards-compliant Temporal client, Claim Check S3 codec, category-based retry policies, heartbeat utilities, and worker health probe as ready-to-use template code.
- Bundled MinIO as a local S3 substitute so Temporal's Claim Check large-payload pattern works in Docker Compose without AWS credentials, removing a developer-environment dependency.
- Designed env-var fallback chains in generated services so the same binary runs locally (env vars), in Docker (Compose-injected vars), and in ECS (SSM Parameter Store) without code changes.
- Replaced Black with Ruff in the scaffold template and wired pre-commit hooks with pinned revs, standardizing linting across every service generated from Cosmos.
- Implemented conditional Jinja2 template rendering and post-generation pruning so the scaffold emits only the infrastructure components (DB, auth, EventBridge, Temporal) that a service actually opts into.
