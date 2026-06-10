---
type: project
slug: farsight-claude-plugins
name: Farsight Claude Code Plugins
employer: Farsight AI
role: author / maintainer
period: 2026-04 — 2026-06
status: active
commits_by_kairi: 14
primary_languages:
  - Markdown
  - TypeScript
technologies:
  - Claude Code
  - Claude Code Plugin System
  - AWS CDK
  - Temporal
  - ECS
domains:
  - tooling
  - developer-experience
  - standards
visibility: internal
---

## What it is

A private Claude Code plugin marketplace for Farsight engineering. Engineers add the `farsight` marketplace source to any repo's `.claude/settings.json` and install plugins that encode Farsight's engineering standards — Temporal workflow patterns, CDK infrastructure patterns, HTTP error handling, and security production readiness — directly into their Claude Code sessions. Instead of living in unread Confluence pages or relying on seniority transfer, each standard is expressed as auditable, executable agent skills.

The marketplace currently ships four plugins: `temporal-standards`, `cdk-standards`, `error-handling-standards`, and `security-readiness`. Each plugin bundles one or more skills (audit, check, migrate, scaffold, convert) plus reference asset documents that Claude reads on-demand.

## My role & ownership

Primary author of the marketplace and all plugins. Initiated the repo and the plugin architecture, authored the `temporal-standards` plugin (the first plugin), then expanded to `cdk-standards`, `security-readiness`, and collaborated on `error-handling-standards`.

## Key contributions

- **`temporal-standards` plugin (v1.0 → v1.1)** — encoded the Temporal standard into four agent skills: `temporal-audit` (assess compliance), `temporal-check` (targeted rule checks), `temporal-migrate` (guided migration to standard patterns), and `temporal-scaffold` (generate standards-compliant boilerplate). Added Pydantic I/O validation, DB unit-of-work, and shared client singleton patterns in v1.1. Added Sentry interceptor pattern (§5.5) in a follow-up.
- **`cdk-standards` plugin** — encoded Farsight's config-driven CDK pattern into skills (`cdk-audit`, `cdk-check`, `cdk-convert`, `cdk-scaffold`) with asset documents covering the full CONFIG_DRIVEN_CDK standard, migration checklist, PR template, and scaffold templates. Added ECS Service Reliability standards (R1-R6) with a dedicated `ecs-reliability-check` skill cross-linked to structural CDK skills so structural conversion is never mistaken for a reliability sign-off.
- **`security-readiness` plugin** — created a `security-worksheet` skill that auto-fills a production security readiness worksheet from CDK stack source code, plus a `security-worksheet-template` command for standalone generation. Added guard logic preventing silent overwrite of existing worksheets.
- Designed the marketplace registration pattern (`extraKnownMarketplaces` in `.claude/settings.json`) enabling one-line opt-in for any Farsight repo.
- Structured each plugin with consistent layout: `.claude-plugin/plugin.json`, `assets/` (reference docs), `commands/` (slash commands), `skills/` (agent skill definitions), and `agents/` (persona-bound reviewers) — establishing the plugin authoring convention for the team.

## Technologies & patterns

- **Claude Code plugin system** — `plugin.json` manifests, marketplace registration, skill activation
- **Agent skills** — audit / check / migrate / scaffold / convert skill pattern per standard domain
- **CDK config-driven pattern** — deploy-config JSON driving CDK stack parameterization
- **ECS reliability standards** — R1-R6 rule set covering health checks, scaling, circuit breaking
- **Temporal standards** — SDK pinning, client singleton, retry categories, Sentry interceptor

## Resume-ready bullets

- Built Farsight's internal Claude Code plugin marketplace, encoding CDK, Temporal, error-handling, and security standards into executable agent skills — replacing unread documentation with tooling that runs inside engineers' existing workflow.
- Authored the `temporal-standards` plugin with four agent skills (audit, check, migrate, scaffold) that assess and remediate Temporal usage compliance against the company standard, reducing onboarding time for new services.
- Delivered the `cdk-standards` plugin covering Farsight's config-driven CDK pattern, including an ECS reliability rule set (R1-R6) with a dedicated reliability-check skill isolated from structural conversion skills to prevent false sign-off.
- Created a `security-readiness` plugin with a worksheet auto-fill skill that reads CDK stack source and populates a production readiness worksheet, turning a manual checklist exercise into an agent-driven output.
- Designed a one-line marketplace registration pattern (`extraKnownMarketplaces` in `.claude/settings.json`) that propagates all plugins to any Farsight repo without per-engineer setup.
