---
type: skills
updated: 2026-06-09
---

# Proficiencies

Evidence-backed across the Farsight microservice stack. "Evidence" points to the projects where the skill was actually applied — see `projects/` for detail.

## Languages
| Language | Level | Evidence |
|----------|-------|----------|
| Python | Primary / production | monitor, retrieval-agent-playground, source-checker, api-relay, email-service, ReSlide, chat-gateway |
| TypeScript | Working | web-app (React/Vite), api-gateway, AWS CDK across services |
| Go | Familiar | SST infrastructure code |
| SQL | Working | PostgreSQL + Alembic migrations across services |

## Backend & frameworks
- **FastAPI / async Python** — service APIs across the stack
- **Celery** — distributed task processing (monitor, email-service, ReSlide)
- **Temporal** — workflow orchestration; authored the internal Temporal standard and migration patterns
- **Pydantic** — schema/validation across services
- **Clean Architecture / DDD** — application/domain/infrastructure layering (per repo CLAUDE.md)

## AI / LLM
- **Multi-provider LLM integration** — Anthropic (Bedrock), Google Gemini, Azure/OpenAI via a shared relay
- **Agentic retrieval** — retrieval-agent-playground (the research agent)
- **Source verification & evaluation** — source-checker: golden-set fixtures, replay harnesses, sweep evaluation
- **Observability for LLM systems** — Langfuse tracing, prompt/telemetry plumbing

## Infrastructure & DevOps
- **AWS** — ECS, RDS, S3, Lambda, SQS, Parameter Store, CodeArtifact
- **AWS CDK** — config-driven CDK pattern (drove migrations across services)
- **SST** — infrastructure-as-code (Temporal infra, examples)
- **Docker / docker-compose** — local dev stacks
- **CI/CD** — GitHub Actions, deploy pipelines with Slack notifications
- **Observability** — Sentry, Prometheus metrics, Langfuse

## Data & messaging
- PostgreSQL, Alembic migrations, Redis, RabbitMQ, SQS, vector search

## Engineering practice
- **Testing rigor** — drove api-relay to 99% coverage (665 tests) with hermetic isolation
- **Standards authorship** — Temporal standard, config-driven CDK standard, error-handling standard, service scaffold template (cosmos), internal Claude Code plugins
- **Developer-productivity tooling** — CLI tools across employers (DDD scaffolds, DB backups at RippleMatch saving ~1,000 eng-hrs/yr; agent plugins at Farsight)
- **Domain-Driven Design** — applied across Farsight, RippleMatch, and shared libraries
- **Code review** — CodeRabbit-integrated review workflows; cross-repo schema consistency

## Earlier-career & additional tech (pre-Farsight)
Real production experience, evidence in `experience/` rather than `projects/`.
| Technology | Where |
|------------|-------|
| Flask | RippleMatch (Tech Lead, Python/Flask backend) |
| Django | Lockard & Wechsler |
| Kafka | RippleMatch |
| Vue | RippleMatch (frontend) |
| RabbitMQ | listed proficiency; Celery brokering |
| MSSQL / T-SQL | Lockard & Wechsler |
| SSRS / SSIS / Tableau | Lockard & Wechsler (BI & reporting) |
| Google Analytics / BigQuery | Lockard & Wechsler (ETL) |
| AWS Lambda, OAuth, GitHub Actions | across roles |
| OCR / RAG | Farsight (document/email services) |

## Domains (summary)
AI/LLM systems · agentic retrieval & source verification · workflow orchestration (Temporal, Celery) · LLM gateway/relay · microservice architecture (DDD/Clean Architecture) · AWS infrastructure & IaC · data/ETL pipelines & BI (earlier career) · developer tooling & engineering standards.

> Maintenance: when a new technology appears in a `projects/*.md` or `experience/*.md` file, add it here with the source as evidence.
