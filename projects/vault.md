---
type: project
slug: vault
name: Vault
employer: Farsight AI
role: contributor
period: 2025-09 — 2025-11
status: maintained
commits_by_kairi: 19
primary_languages:
  - Python
  - TypeScript
technologies:
  - FastAPI
  - AWS CDK
  - ECS Fargate
  - RDS PostgreSQL
  - EventBridge
  - S3
  - Docker Compose
  - AWS Parameter Store
  - Alembic
domains:
  - infrastructure
visibility: internal
---

## What it is

Vault is a Farsight microservice that manages document storage with presigned S3 URL generation and an EventBridge publish pipeline for downstream consumers. It follows the standard Farsight service architecture: FastAPI application layer, clean domain / application / infrastructure separation, CDK-defined AWS infrastructure (ECS Fargate, RDS, VPC, EventBridge), and SSM Parameter Store for runtime configuration.

## My role & ownership

Contributor during the service's early development phase (2025-09 to 2025-11). Focused on infrastructure configuration and two application-layer features, then handed off as the service stabilized.

## Key contributions

- Implemented the internal presigned S3 URL API: `feat(api): add internal API endpoints for presigned URL generation`, including expiration configuration and route documentation (`refactor(routes): enhance documentation and clarify purpose of presigned URL endpoint`).
- Enhanced environment validation and added AWS SSM connectivity checks at startup so misconfigured deployments fail fast with actionable errors.
- Added Docker Compose configuration for local service development.
- Tuned ECS service settings and VPC lookup configuration across multiple infra iterations, including an RDS instance type correction from SMALL to MEDIUM.
- Created the EventBridge event bus for vault update publication (PR #19 / DEV-248).

## Technologies & patterns

- **AWS CDK** — infrastructure stack authoring (ECS / RDS / VPC / EventBridge)
- **SSM Parameter Store** — startup connectivity validation pattern
- **FastAPI** — presigned URL route with internal access control
- **S3 presigned URLs** — time-bounded object access generation
- **EventBridge** — event bus for downstream service notification

## Resume-ready bullets

- Delivered presigned S3 URL generation endpoints for Vault's internal API, including expiration configuration and startup SSM connectivity validation that surfaces misconfiguration before requests arrive.
- Configured ECS service settings, VPC lookup, and RDS instance sizing via AWS CDK; provisioned an EventBridge event bus enabling downstream services to subscribe to vault document updates.
