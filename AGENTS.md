# AGENTS.md

## Project

Discord bot that watches YouTube Atom feeds, routes uploads, caches transcripts, and optionally serves Reddit memes.
Python handles the local bot; `cloudflare/` is the always-on Worker and D1 deployment.

## Commands

- Install Python: `python -m pip install -r requirements.txt`
- Run local bot: `python run.py`
- Test Python: `python -m pytest -q`
- Run container: `docker compose up --build -d`
- Install Worker: `cd cloudflare && npm ci`
- Check Worker: `cd cloudflare && npm run check`
- Test Worker: `cd cloudflare && npm test`

## Structure

- `discord_bot.py` / `run.py` — local bot lifecycle and Discord interactions.
- `youtube_client.py` / `youtube_transcript.py` — feeds and caption retrieval.
- `caption_cache.py` / `gemini_summary.py` — transcript cache and grounded summaries.
- `cloudflare/src/index.ts` — Worker feeds, interactions, intake, pages, and summaries.
- `cloudflare/migrations/` — ordered D1 schema changes.
- `cloudflare/scripts/` — command registration and route seeding.
- `test_*.py` / `cloudflare/test/` — Python and Worker regression suites.

## Rules

- Never commit `.env`, Discord IDs, route seeds, tokens, Worker secrets, or local Wrangler state.
- Apply D1 migrations before deploying code that depends on them.
- Upload notifications must not wait for captions or summaries.
- Preserve idempotency, checkpoints, retry schedules, dead jobs, and restart-safe queues.
- YouTube caption retrieval must remain honest about disabled/private/regional failures.
- Ground summaries and follow-up answers only in the cached transcript.
- Keep local feed polling opt-in; the Worker is the default always-on owner.

## Before finishing

- Run Python and Worker test suites.
- Run `npm run check` in `cloudflare/` for Worker changes.
- Verify `/health` and relevant Discord permissions manually for deployment changes.
