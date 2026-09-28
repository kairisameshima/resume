---
type: project
slug: auth-gateway
name: Auth Gateway
employer: Farsight AI
role: designer and primary author (replatform of api-gateway)
period: 2026-09 — present
status: active
commits_by_kairi: 57
primary_languages: [TypeScript, Python]
technologies: [AWS API Gateway HTTP API v2, AWS Lambda, Lambda@Edge, DynamoDB, CloudFront, AWS WAF, VPC Link, AWS Cloud Map, AWS CDK, AWS SSM, WorkOS, JWT (RS256), Docker]
domains: [authentication, edge security, session management, service-to-service auth, infrastructure as code]
visibility: internal
---

# Auth Gateway

## What it is
Serverless edge authentication for the Farsight platform, carved out of `api-gateway` to replace the Effect.ts forwarder running on ECS. Users sign in through WorkOS (enterprise SSO, magic links). A Lambda request authorizer on API Gateway HTTP API v2 then mints a short-lived RS256 internal JWT that backend services verify with a shared library.

## My role & ownership
Designed the replatform and wrote nearly all of the code (57 commits on the main working branch, 2026-09). The work is in review as a stack of open PRs (#4–#8) and has not merged. The legacy `api-gateway` service stays in place until backends cut over.

## Key contributions
- Built an internal identity library for signing and verifying RS256 tokens in TypeScript and Python, kept in lock-step by shared conformance vectors, with separate signing keys for user-facing (north-south) and service-to-service (east-west) tokens (PR #4)
- Built the Lambda request authorizer, the WebSocket edge authorizer, and the login/callback/logout session handlers backed by a DynamoDB session registry (PR #5)
- Hardened the sign-in flow: PKCE, exact-match redirects, login and refresh bound to the browser that started them, revocation when a refresh token is replayed, and token delivery through a single-use code instead of a URL or cookie (PR #5)
- Made token minting fail closed when the tenant cannot be resolved, and unified tenant handling on a single `organization_id` across tenant modes
- Added a service-to-service token exchange that logs every mint and lets each service declare which callers it accepts; it replaced the earlier `/auth/grant` endpoint (PR #6)
- Wrote the CDK stacks for the gateway and a fixture service used to validate it end to end, including the plat-sandbox stage with a custom domain and CloudFront origin (PR #7, DEV-2086)
- Wrote the backend adoption guide and runbooks for TLS termination and emergency response, and set up CI (PR #8)

## Technologies & patterns
- **Edge stack**: CloudFront + WAF in front of API Gateway HTTP API v2, a VPC Link to private backends, and AWS Cloud Map for target lookup
- **Lambda authorizer**: verifies the WorkOS bearer against the session registry, then injects a short-lived internal JWT so backends never talk to WorkOS
- **Split trust domains**: distinct RS256 signing keys for north-south (user) and east-west (service) tokens
- **Cross-language verifier parity**: TypeScript and Python verifiers tested against the same conformance vectors
- **Local mirror**: a local proxy runs the deployed gateway shape against real SSM, DynamoDB, and WorkOS (`make up`)

## Resume-ready bullets
- Designed and built a serverless edge-auth replatform (API Gateway HTTP API v2, Lambda authorizer, DynamoDB sessions, CloudFront/WAF) that mints short-lived RS256 identity tokens for users and services
- Hardened the sign-in flow with PKCE, exact-match redirects, browser-bound refresh, refresh-replay revocation, and single-use-code token handoff, with minting that fails closed on unresolved tenants
- Built matching TypeScript and Python token verifiers kept in lock-step by shared conformance vectors, with separate signing keys for user and service-to-service trust
