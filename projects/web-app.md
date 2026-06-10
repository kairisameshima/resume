---
type: project
slug: web-app
name: Web App
employer: Farsight AI
role: Contributor
period: 2025-08 — 2026-06
status: active
commits_by_kairi: 40
primary_languages:
  - TypeScript
  - React
technologies:
  - React 18
  - Vite
  - TanStack Router
  - TanStack Query
  - Orval (OpenAPI codegen)
  - Zod
  - Tailwind CSS
  - pnpm
  - Vitest
  - MSW (Mock Service Worker)
  - AWS CDK (infra)
  - i18next
domains:
  - Internal AI research tool frontend
  - File upload and async job polling
  - Real-time slide generation UI
  - Monitor management UI
visibility: internal
---

# Web App

## What it is

The primary React/TypeScript frontend for Farsight AI's research platform, serving features across AI-driven deliverables (slide generation), document analysis, monitor configuration, and source verification. Built with Vite, TanStack Router for type-safe file-based routing, and Orval for generating typed API hooks from OpenAPI schemas.

## My role and ownership

Contributor on a team of ~six frontend engineers (jon@farsight-ai.com is the dominant committer with 753 commits; Kairi sits at 40). Owned specific feature vertical slices rather than the app as a whole.

## Key contributions

- **Source Checker tab (2026-03, DEV-226 area):** Shipped a complete `/source-checker` route from scratch — file upload (base64 through API gateway), job submission, conditional 3-second `refetchInterval` polling until terminal status, and a results display with `ClaimCard`, `PageCard`, `ScoreDonut`, and `StatusBadge` components. Added Zod schemas for all response shapes, i18n via a new `source-checker` namespace, and MSW handler stubs for local development. Sidebar `NavLink` gated behind `import.meta.env.DEV`.
- **File conversion support (DEV-210, 2025-12):** Added multi-format file attachment support (PPTX, DOCX, PDF) for research-agent inputs; updated `FileIcon` component with filename-based icon rendering and extended its test coverage.
- **Supporting documents upload (DEV-184, 2025-12):** Built `SupportingDocsList` component and wired supporting-document upload flow; refactored MSW API mock handlers and updated OpenAPI schemas to match the new endpoint contract.
- **Slide retry UX (2025-12):** Implemented retry-slide functionality for failed slide generation jobs — added retry action on `GeneratedSlideItem`, refactored slide state machine, and fixed download-button visibility guard to require an S3 key.
- **Multi-slide frontend (DEV-112, 2025-11):** Added Phase 6 of the multi-slide rollout — front-end support for generating, displaying, and downloading multiple slides per request.
- **Monitor UI (2025-08–09):** Bootstrapped the monitors feature — generated Orval API hooks and TypeScript types from the Monitor OpenAPI spec, built route components for monitor listing and creation, added notification-interval field to `AdvancedCreateForm`, improved form validation across `NotifierFields` and `PromptField`, and updated `EventTable` sorting to use `last_notified_at`.

## Technologies and patterns

TanStack Router for type-safe file-based routing; TanStack Query (`useQuery`, `refetchInterval`) for async job polling; Orval for OpenAPI-driven hook/type generation; Zod for runtime schema validation of API responses; MSW for local API mocking; Tailwind CSS + `ui-common` component library; i18next for full string externalization; Vitest for unit tests. Followed the codebase's `fetcher` pattern for all HTTP calls and the existing `use-me-api` hook conventions.

## Resume-ready bullets

- Delivered the Source Checker feature vertical end-to-end in the React/TS frontend: file upload, Zod-typed job polling with adaptive `refetchInterval`, and a multi-component results view with claim verification scores.
- Extended the research-agent document ingestion UI to support PPTX, DOCX, and PDF formats, including filename-aware icon rendering and MSW mock handler updates.
- Built supporting-document upload and `SupportingDocsList` component, coordinating schema changes across MSW handlers and the OpenAPI contract.
- Bootstrapped the monitors feature: generated typed Orval API hooks from the Monitor OpenAPI spec and built route components for monitor creation and event tracking.
---
