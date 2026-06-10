---
type: project
slug: public-market-update
name: Public Market Update
employer: Farsight AI
role: Contributor
period: 2026-04 — 2026-05
status: active
commits_by_kairi: 9
primary_languages:
  - Python
  - TypeScript
technologies:
  - Python (Quart / aiohttp)
  - Gemini (Vertex AI streaming)
  - API Relay (internal SSE proxy)
  - TypeScript / React
  - TanStack Query
  - Zod
  - Socket.IO
  - Docker
  - AWS / GCP Parameter Store
domains:
  - AI-powered slide automation
  - Streaming LLM responses
  - Source verification
  - Office add-in (Excel/PowerPoint)
visibility: internal
---

# Public Market Update

## What it is

An AI-powered slide-automation service (Python Quart backend + TypeScript Office add-in) that generates M&A public-market-update presentations by retrieving live research, running Gemini-powered analysis, and inserting results directly into PowerPoint/Excel templates. The backend integrates with a retrieval agent for citation-backed research and routes LLM calls through an internal API relay for centralized credential management and observability.

## My role and ownership

Contributor on a large team (victor@farsight-ai.com leads with ~1895 commits; Kairi contributed 9 targeted commits). Work was scoped to two focused bug-fix/feature tracks: fixing a production streaming breakage and surfacing source-checker progress in the Office add-in.

## Key contributions

- **Gemini streaming via API relay (DEV-859, 2026-04):** Routed all three Gemini streaming callers (`GeminiClient.stream`, `LLMCallService`, retrieval-agent path) through the internal `/api/relay/gemini/stream` SSE endpoint instead of calling `generate_content_stream` directly against Vertex AI. Added `build_gemini_stream_request()` to serialize SDK objects and `stream_gemini_sse()` on `APIRelayClient` to consume SSE events and yield rehydrated `GenerateContentResponse` objects, preserving `thought_signature` and `function_call` parts across the round-trip.
- **aiohttp 128 KB line-limit fix (DEV-881, 2026-04):** Diagnosed and fixed a production breakage where the research-info tool's `response.content.readline()` loop silently truncated citation events that exceeded aiohttp's 128 KB per-line cap. Switched to `response.content.read(65536)` with a manual newline-split framing loop; seeded `ranges: []` on source init to match the updated citation payload shape. Added structured logging (`START` / `HTTP-error` / `STREAM-ERROR` / `OK` / `EXCEPTION`) with room, elapsed, and query-prefix tags, and replaced bare `print()` in the tool executor with `logger.exception()`.
- **useJob polling for per-claim source-checker updates (DEV-953, 2026-05):** Surfaced granular per-claim verification progress in the Office add-in's Sources tab. Enabled `refetchInterval=3s` on `useJob` in `SourcesResults`, stopping the poll once the job reaches a terminal status. Tracked new backend substages (`extracting_claims`, `preparing_evidence`, `verifying_claims`) through `jobStatusSchema` enum updates, `SourceJobStatus` context type, `isCurrentSlideProcessing` guard, and a `sourceCheckerStatus` utility exporting user-facing `statusLabel` strings.

## Technologies and patterns

aiohttp streaming with manual SSE framing (replacing `readline()` with chunked `read()`); Gemini SDK (`generate_content_stream`) serialized for relay round-trips via a custom `build_gemini_stream_request()` helper; TanStack Query `refetchInterval` for adaptive polling; Zod schema enum extension for substage tracking; structured Python logging replacing scattered `print()` calls. Quart (async Flask-compatible) for the backend server; Socket.IO for real-time add-in communication.

## Resume-ready bullets

- Fixed a silent production streaming failure in the retrieval-agent SSE consumer by diagnosing aiohttp's 128 KB readline cap and replacing it with chunked `read()` + manual framing — restored citation rendering without any API contract changes.
- Routed all Gemini streaming calls through an internal API relay, adding SSE serialization/deserialization helpers that preserved `thought_signature` and `function_call` parts across the round-trip.
- Wired adaptive `useJob` polling in the Office add-in source-checker UI to surface per-claim verification substages in real time, stopping the poll automatically on terminal job states.
---
