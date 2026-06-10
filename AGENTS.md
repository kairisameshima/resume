# Instructions for Coding Agents

You are maintaining Kairi's **career source of truth**. Two jobs: **record** new facts, and **generate** tailored resumes from them. Read this fully before touching anything.

## Golden rules

1. **Facts vs. presentation.** `projects/`, `skills/`, `accomplishments/`, `experience/`, `profile/` hold *facts*. `resumes/` holds *outputs*. Never edit facts to fit a resume narrative — fix reality in the facts, then regenerate the output.
2. **Evidence or it doesn't go in.** Every claim should trace to something verifiable: a commit count, a DEV/Jira ticket, a PR number, a date range, a metric. If you can't source it, mark it `// unverified` and surface it to Kairi.
3. **Frontmatter is mandatory.** Every fact file opens with YAML frontmatter per [`.meta/schema.md`](.meta/schema.md). Validate before saving.
4. **No invention.** Do not inflate scope, fabricate metrics, or assign ownership Kairi didn't have. Understatement beats a claim that won't survive an interview.
5. **Metric units.** Kairi uses metric and ISO dates (`YYYY-MM` or `YYYY-MM-DD`).

## Recording new work

When Kairi ships something or you're asked to capture work:

1. Identify the project. Does `projects/<slug>.md` exist?
   - **Yes** → append to `Key contributions` and, if it's resume-worthy, `Resume-ready bullets`. Bump `commits_by_kairi` and `period` in frontmatter if you have fresh git data.
   - **No** → create it from the template in `.meta/schema.md`.
2. Source the facts. Prefer git over memory:
   ```bash
   git -C ~/Github/<repo> log --all --author="kairi@farsight-ai.com" --format='%ad %s' --date=short
   ```
3. If the work is a cross-project highlight (a standard you set, a big migration, a hard bug), also add a one-line entry to `accomplishments/highlights.md`.

## Generating a tailored resume

1. Read the job target Kairi gives you (role, seniority, company, emphasis).
2. Read `profile/identity.md`, `profile/education.md`, ALL `experience/*.md` (Farsight + prior roles), the relevant `projects/*.md`, `skills/proficiencies.md`, and `accomplishments/highlights.md`.
3. Select and rank: pull the `Resume-ready bullets` that match the target. For Farsight, draw detailed bullets from `projects/*.md`; for pre-Farsight roles (RippleMatch, Lockard & Wechsler, Summit Sync) the bullets live in `experience/*.md`. Lead with the domain the role cares about; include education at the end.
4. Fill `resumes/_template.md`, write to `resumes/<role-slug>-<YYYY-MM-DD>.md`.
5. Keep every bullet traceable to a fact file. Do not introduce claims absent from the facts.
6. Report which projects/bullets you included and which you cut, and why.

## Project slugs (current)

Match repo directory names under `~/Github/` so git verification is one step. See `projects/` for the live set.

## When facts conflict

If git history contradicts a written claim (wrong dates, ownership, scope), trust git and flag the discrepancy to Kairi rather than silently overwriting.
