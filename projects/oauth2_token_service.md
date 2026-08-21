---
type: project
slug: oauth2_token_service
name: OAuth2 Token Service
employer: Farsight AI
role: primary contributor (second-most commits; co-designed the security model)
period: 2026-07 — present
status: active
commits_by_kairi: 153
primary_languages:
  - Python
  - TypeScript
technologies:
  - FastAPI
  - PostgreSQL
  - SQLAlchemy
  - Alembic
  - Redis
  - AWS CDK
  - ECS Fargate
  - AWS KMS
  - AWS Secrets Manager
  - PyJWT
  - Sentry
  - WorkOS (via Farsight API Gateway)
  - PitchBook OAuth
domains:
  - authentication
  - OAuth2 / delegated authorization
  - secrets and key management
  - multi-tenant SaaS integrations
  - production readiness
visibility: internal
---

# OAuth2 Token Service

## What it is

A FastAPI service that owns third-party OAuth integrations (PitchBook, with the provider surface built to onboard more) on behalf of Farsight's users. It runs the OAuth authorize/callback/token/revoke flows, stores vendor tokens encrypted at rest, renews them proactively, and issues short-lived internal "delegation grants" so downstream services (research-agent) can act on a user's connected vendor account without ever handling the vendor credentials themselves. Reached production on 2026-08-05.

## My role & ownership

Second-highest committer on the repo (153 commits behind the lead author's 166) and co-designer of the service's security model — the delegation-grant flow, the token-encryption boundary, and the CDK deployment consolidation. Drove the service from a JWT-only rework through the multi-stage ("slug") deployment model to the single prod-ready CDK stack that shipped.

## Key contributions

- **Designed and shipped the delegation-grant model (DEV-1522/1517/1519):** short-lived, mintable/redeemable/renewable grants that let research-agent act on a user's PitchBook connection without exposing vendor tokens, with return-origin parameters enforced on every route.
- **Envelope-encrypted vendor tokens at the repository boundary (DEV-1527):** dedicated KMS CMK per deployment, task-role-only decrypt grants, pinned `KeyId`, a reveal-volume CloudWatch alarm, and a fail-closed refresh path if the key is unreachable.
- **Consolidated CDK deployments** from a "frozen stack + per-slug stacks" model down to one main deployment with per-stage Postgres/Redis and a stage-keyed named-consumer allowlist, then retired the frozen stack entirely (DEV-1556 series).
- **Drove the vendor cutover:** moved the router to the vendor-registered hostname, added a CNAME cutover path with graceful fallback to the previous occupant, and made the OAuth callback deployable against the registered redirect URI without breaking vendor-side registration.
- **Led the DEV-1607 hardening pass that gated the service for prod promotion:** cleared 13 npm advisories and all outstanding Python/secret-scan advisories, added a PR-gated dependency scan, and stopped vendor exceptions from chaining into Sentry.
- **Shipped the prod launch itself (2026-08-05):** organization-scoped token storage at the repository layer, refused a main deployment with no configured providers or consumer ingress, wrote the vendor-client-config promotion script between stages, and admitted research-agent as prod's first named consumer.
- **Diagnosed and fixed PitchBook's OAuth quirks (DEV-1641):** the vendor's refresh window is an absolute deadline from the original connect time (not sliding on renewal) — renewed inside the shortened session window, served the still-valid access token when a renewal attempt is refused rather than failing the caller, and stopped reporting a connection as "connected" once it's unrecoverably expired.
- **Found and fixed a live secret leak (DEV-1642/follow-up):** delegation grants were being sent to Sentry on error paths; added redaction plus a test that guards the redaction boundary, and fixed a related local-dev bug where the token-encryption KEK wasn't declared and Redis was reachable off-loopback.
- **Cut prod error noise:** collapsed a fault path that was reporting the same unhandled error twice, and reworked the Alembic migration path to serialize across concurrently starting ECS tasks instead of racing.

## Technologies & patterns

- **FastAPI + SQLAlchemy/Alembic + PostgreSQL** for the token store, with **Redis** for short-lived grant/session state
- **AWS KMS customer-managed CMK** envelope encryption for vendor tokens at rest, task-role-scoped decrypt
- **Config-driven AWS CDK** consolidating from a multi-stack "slug" deployment model to one parameterized stack per stage
- **Delegated-authorization pattern** — internal short-lived grants standing in for vendor OAuth tokens across service boundaries
- **Sentry** with explicit secret redaction on error-reporting paths
- **Vendor-quirk handling** — absolute (non-sliding) OAuth refresh deadlines, SSO-only authorize flows

## Resume-ready bullets

- Co-designed and shipped a production OAuth2 delegated-authorization service: short-lived internal grants let downstream services act on a user's third-party vendor connection without ever touching the vendor's tokens.
- Designed the token-encryption boundary — per-deployment KMS CMK envelope encryption, task-role-only decrypt, fail-closed refresh — protecting every stored vendor credential at rest.
- Consolidated a multi-stack "slug" deployment model into one CDK stack per stage, then led the security-gate hardening (dependency/secret-scan advisories cleared) that unblocked the 2026-08-05 production launch.
- Diagnosed a vendor-specific OAuth failure mode (PitchBook's refresh window is an absolute deadline, not sliding) and shipped a renewal strategy that serves the still-valid token rather than failing the caller mid-window.
- Found and closed a live secret leak — delegation grants reaching Sentry on error paths — with a redaction test guarding the boundary going forward.
