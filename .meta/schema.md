# Schema & Conventions

Every fact file opens with YAML frontmatter. Below is the contract per file type, plus the canonical project template.

## File types

### `profile/identity.md`
```yaml
---
type: identity
name: Kairi
location: New York, USA
timezone: America/New_York
links:
  github: <url>
  linkedin: <url>
  email: <email>
---
```

### `experience/<employer>.md`
```yaml
---
type: experience
employer: Farsight AI
title: Backend Engineer
location: New York (remote)
period: 2025-03 — present
employment: full-time
---
```

### `projects/<slug>.md`
`<slug>` matches the repo directory name under `~/Github/` so git verification is one command.
```yaml
---
type: project
slug: monitor
name: Monitor
employer: Farsight AI
role: Owner / primary backend engineer
period: 2025-05 — 2026-05
status: active            # active | maintained | archived | one-off
commits_by_kairi: 477     # from git, author kairi@farsight-ai.com
primary_languages: [Python]
technologies: [Celery, PostgreSQL, Alembic, Temporal, AWS CDK]
domains: [orchestration, monitoring]
visibility: internal      # internal | public | personal
---
```

### `skills/proficiencies.md` and `accomplishments/highlights.md`
```yaml
---
type: skills            # or: accomplishments
updated: 2026-06-09
---
```

### `profile/education.md`
```yaml
---
type: education
updated: 2026-06-09
---
```

### Pre-employer roles without a local repo
Roles before the git-tracked work (RippleMatch, Lockard & Wechsler, Summit Sync) live as `experience/<employer>.md` with `source: existing resume` in frontmatter. They carry `Resume-ready bullets` directly (no `projects/` backing, since there's no local repo to mine). Add an optional `stack: [...]` array to experience frontmatter.

## Canonical project file template

```markdown
---
type: project
slug: <repo-dir-name>
name: <Display Name>
employer: Farsight AI
role: <ownership level>
period: <YYYY-MM> — <YYYY-MM | present>
status: active
commits_by_kairi: <n>
primary_languages: [<...>]
technologies: [<...>]
domains: [<...>]
visibility: internal
---

# <Display Name>

## What it is
1–2 sentences: what the service/project does and why it exists.

## My role & ownership
What Kairi owned vs. contributed to. Be precise — "owner", "co-owner", "contributor".

## Key contributions
- Specific, evidence-backed. Cite DEV-#### tickets / PR #s where known.
- Quantify (coverage %, test counts, latency, scale) where the data supports it.

## Technologies & patterns
- Languages, frameworks, infra, and the *patterns* applied (Clean Architecture,
  config-driven CDK, Temporal workflows, Celery workers, etc.).

## Resume-ready bullets
- Tight, achievement-oriented, past-tense, quantified. These get lifted verbatim
  (or lightly tailored) into generated resumes. Aim for 2–5 strong bullets.
```

## Style rules

- **Past tense, active voice, achievement-first.** "Drove api-relay to 99% test coverage (665 tests)" not "Was responsible for testing."
- **Quantify or qualify.** Numbers when you have them; honest scope when you don't.
- **One fact, one home.** Don't duplicate the same accomplishment across many files; cross-reference instead.
