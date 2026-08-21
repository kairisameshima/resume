---
type: project
slug: mega-city-one
name: Mega-City One (Docket + Dredd — System of Judgement)
employer: Farsight AI
role: primary contributor (top committer)
period: 2026-06 — 2026-07
status: active
commits_by_kairi: 105
primary_languages:
  - Python
  - TypeScript
technologies:
  - FastAPI
  - AWS CDK (config-driven)
  - ECS Fargate
  - AWS Lambda
  - S3
  - AWS Glue
  - Amazon Data Firehose
  - Amazon Athena
  - Amazon Aurora / RDS Proxy
  - AWS KMS
  - API Gateway (HTTP API)
  - WorkOS (JWT authorizer)
  - CloudWatch / SNS / AWS Chatbot
domains:
  - event sourcing / append-only ingestion
  - data lake
  - observability
  - load testing
  - infrastructure
visibility: internal
---

# Mega-City One (Docket + Dredd)

## What it is

The backend of Farsight's "System of Judgement": an append-only event store (`docket`) that records every create/edit/copy/delete made to a PowerPoint artifact, plus a pattern-mining service (`dredd`) that reads a derived surface of that log to judge reuse, acceptance, and systematic corrections of AI-generated slide content. A companion "witness" component (in the PMU/add-in repo) captures the events at the source and submits them to docket's ingest API.

## My role & ownership

Top committer on the repo (105 commits, ahead of the two other contributors) from the initial commit (2026-06-24) through the burst-load proof (2026-07-22). Built the ingest pipeline's infrastructure end-to-end and owned its production-readiness hardening.

## Key contributions

- **Stood up `dredd` on a config-driven CDK Fargate + public ALB stack** adopting the Cosmos microservice-template layout, then migrated its CI from GitHub Actions to CodePipeline/CodeBuild and renamed the deployed stack family (`dredd-*` → `judgement-*`) as the product scope clarified.
- **Designed the config-driven internal network topology for behind-gateway stacks (DEV-1363/1402/1403):** pinned ALB + tasks to private-with-egress subnets, opened Service Connect ingress from the API Gateway security group directly into the judgement task, then dropped the now-vestigial internal ALB entirely once Service Connect proved sufficient.
- **Built the docket ingest pipeline (DEV-1368/1369/1370/1371):** a versioned JSON-Schema ingest contract aligned to the witness engine's types, an HTTP API front door with a WorkOS JWT authorizer, a validator Lambda doing contract validation and verified-claim stamping, and an S3/Glue/Firehose Parquet lake with partitioning by ingest time.
- **Added dedupe-on-read Athena views (DEV-1372)** registered via a CDK custom resource, and tightened the registrar's IAM plus workgroup result-set encryption on review.
- **Hardened the ingest path for production traffic:** poison-record rejection with honest failure counts instead of silent drops, partial-accept batch semantics for mixed-validity batches (DEV-1420), strict envelope validation with a fail-closed 500 and bounded rejection payloads, and KMS-encrypted lake storage with the validator Lambda scoped to only the key operations it needs.
- **Proved the ingest path's scalability claim rather than asserting it (DEV-1376):** built a load-test harness with a live dashboard, byte-size guards, and env-sourced config, and a client-side WorkOS PKCE sign-in so the harness could exercise the real auth path — then ran a burst-load test against the sandbox lake to validate throughput under load.
- **Shipped observability from day one (DEV-1375):** CloudWatch alarms and a dashboard on accept/reject/forward counts, wired through SNS to Slack via AWS Chatbot with environment-tagged messages, plus a corrected Firehose delivery-failure alarm threshold (ratio, not raw percentage).
- **Wrote an end-to-end synthetic replay test (DEV-1374)** and a local authenticated forward-proxy so the full deck-session ingest path could be exercised without a real Office add-in client.
- **Wrote the ML-engineer handoff doc** covering agent location, ingest contract, and run/test/deploy steps, keeping it current as the pipeline moved from sandbox to a stable `farsight-dev` stage with its own custom domain.

## Technologies & patterns

- **Config-driven AWS CDK** — Fargate services and serverless ingest infra sharing one parameterized stack pattern, following the Cosmos scaffold
- **Serverless event ingestion** — API Gateway HTTP API → Lambda validator → Firehose → S3/Glue Parquet lake, with Athena views for dedupe-on-read queries
- **Behind-gateway networking** — Service Connect ingress from a shared API Gateway security group, replacing per-service ALBs
- **WorkOS JWT authorization** at both the ingest API and a load-test harness's own PKCE sign-in flow
- **Observability-as-code** — CloudWatch alarms + SNS + Chatbot wired into CDK alongside the resources they monitor
- **Load testing as verification** — a purpose-built harness proving throughput/scalability claims rather than asserting them

## Resume-ready bullets

- Built the ingestion backbone of an append-only event-sourcing system for AI-generated PowerPoint provenance: API Gateway → JWT-authorized validator Lambda → Firehose → S3/Glue Parquet lake, with Athena dedupe-on-read views.
- Designed the behind-gateway network topology for a new serverless stack — Service Connect ingress from the shared gateway security group — eliminating a per-service ALB and its vestigial internal listener.
- Hardened the ingest path for production traffic: poison-record rejection with honest failure counts, partial-accept batch semantics, fail-closed strict envelope validation, and KMS-encrypted storage scoped to least-privilege Lambda IAM.
- Proved the pipeline's scalability claim with a purpose-built load-test harness (live dashboard, WorkOS PKCE auth, byte-size guards) rather than shipping an unverified throughput assertion.
- Shipped full observability (CloudWatch alarms, SNS-to-Slack via Chatbot) alongside the ingest infrastructure itself, not as a follow-up.
