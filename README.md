# Kairi — Career Source of Truth

This repository is a **structured, evidence-backed record of professional work** — built so that coding agents (and humans) can record accomplishments, projects, and proficiencies over time, then assemble tailored resumes on demand.

It is **not** a resume. It is the *source of truth a resume is generated from.*

## How it works

```
profile/        Who I am — identity (contact, summary, links) + education
experience/     Roles, one file per employer (Farsight + RippleMatch, Lockard & Wechsler, Summit Sync)
projects/       The heart: one rich file per project, with evidence
skills/         Proficiencies (languages, frameworks, tools) and domains
accomplishments/ Cross-project, quantified highlights — bullet-ready
resumes/        Generated, tailored outputs (disposable)
.meta/          The schema + conventions agents must follow
```

## The core idea: facts vs. presentation

- **Facts** live in `profile/`, `experience/`, `projects/`, `skills/`, `accomplishments/`. They are durable, evidence-backed (commit counts, tickets, dates, technologies), and edited only when reality changes.
- **Presentation** lives in `resumes/`. Each file there is a *tailored output* assembled from the facts for a specific job, audience, or format. These are disposable — regenerate freely.

An agent generating a resume **reads facts and writes a new file in `resumes/`**. It never edits the facts to fit a narrative.

## Quick start for a human

- "Generate a resume tailored to a backend/infra staff role" → see `AGENTS.md` → it reads `projects/`, `skills/`, `accomplishments/`, fills `resumes/_template.md`, writes `resumes/<role>-<date>.md`.
- "I shipped something new" → add or update the relevant `projects/*.md` file, then refresh `accomplishments/highlights.md`.

## Conventions

Every fact file carries YAML frontmatter. The contract is in [`.meta/schema.md`](.meta/schema.md). Agents working in this repo must read [`AGENTS.md`](AGENTS.md) first.
