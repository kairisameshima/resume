---
type: project
slug: api-gateway
name: API Gateway
employer: Farsight AI
role: contributor (ops, routing, WAF hardening)
period: 2025-08 — 2026-04
status: active
commits_by_kairi: 33
primary_languages: [TypeScript]
technologies: [Node.js, Effect.ts, WorkOS, JWT, AWS SSM, Sentry, Docker, AWS CDK, Vitest, pnpm]
domains: [authentication, reverse proxy, request forwarding, session management, WAF configuration, microservice routing]
visibility: internal
---

# API Gateway

## What it is
A TypeScript reverse-proxy and authentication gateway that served as the central entry point for the Farsight AI microservices platform. It authenticated users through WorkOS (SSO, magic links, OTP, OAuth providers), managed sealed-cookie and token-mode sessions, injected short-lived JWTs into upstream requests, and routed traffic to downstream services including vault, monitor, chat-gateway, source-checker, and reslide.

## My role & ownership
Contributor responsible for critical request-forwarding correctness fixes, WAF policy management, and microservice onboarding. Maintained the routing layer as new services were added to the platform.

## Key contributions
- Fixed a body-forwarding bug on DELETE requests (DEV-885): the gateway silently dropped request bodies on DELETE, breaking downstream services that relied on them; fixed at both the forwarder layer and the incoming-body read path
- Added the source-checker microservice to the gateway routing configuration, enabling the new service to receive authenticated, JWT-injected traffic from the gateway
- Authored and resolved a WAF bypass rule for source-checker upload endpoints alongside existing vault and S3 rules, unblocking file upload flows that were being rejected by the CloudFront WAF
- Added the reslide microservice endpoint and updated API endpoint URLs as services migrated to private DNS
- Added chat microservice route with support for unauthenticated health endpoint, enabling the chat service to integrate with the platform auth layer
- Enhanced logging, request tracing, and microservices configuration at project inception (2025-08), establishing the correlation ID pattern used across all distributed request traces

## Technologies & patterns
- **Effect.ts functional core**: HTTP server and client abstraction built on `@effect/platform` with typed error channels, eliminating unhandled promise rejections in the forwarding layer
- **JWT token injection**: gateway minted short-lived HS256 JWTs (`{ user_id, email, organization_id }`) forwarded as `Authorization: Bearer` to every upstream service, decoupling microservice auth from WorkOS
- **Session management**: WorkOS sealed cookies (`wos_session`) with automatic refresh and in-memory refresh-token store per ECS task
- **WAF policy layering**: CloudFront WAF exemptions scoped per endpoint path pattern, with source-checker bypass added alongside existing vault/S3 rules
- **Parameter Store config**: secrets loaded at startup from SSM hierarchy `/{repo}/{stage}/{branch}/{param}`, with environment variable overrides for local development
- **Unauthenticated path allowlist**: structured bypass for OpenAPI docs, health checks, and upload endpoints via `isUnauthenticatedPath` helper

## Resume-ready bullets
- Fixed a silent request-body drop on DELETE forwarding (DEV-885), restoring correct proxy semantics for downstream services relying on DELETE payloads
- Onboarded the source-checker microservice to the gateway routing and WAF configuration, including scoped CloudFront WAF bypass rules for file upload paths
- Contributed foundational request-tracing infrastructure — correlation ID generation, structured logging, and microservice config management — establishing the distributed observability pattern used across the platform
