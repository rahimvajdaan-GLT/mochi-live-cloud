# Mochi AI Pet — browser / cloud edition

## What works
- Responsive 9:16 LIVE visual overlay (`/`) with animated pet, evolution, stats, gift count and in-game captions.
- Browser control panel (`/dashboard`) with simulated Roses, gifts and free actions (feed/play/rest/hello).
- Runs without installing desktop programs, with **local browser demo mode** even before any external service setup.
- Serverless `/api/state` public read and `/api/event` key-protected write; optional cloud persistence via Upstash Redis REST.
- Optional server-side OpenAI-generated pet dialogue (`OPENAI_API_KEY`) with a short family-friendly prompt; browser Web Speech API reads pet dialogue aloud. This is AI-written text + browser speech, **not** OpenAI audio TTS. Turn on voice in dashboard and allow browser audio on overlay.

## Important current limits
- **No TikTok account login or direct real-time TikTok gift reception is included.** Connecting TikTok LIVE gift streams can require an approved integration, a desktop event listener, or an external hosted service. Do not share TikTok credentials with an unverified vendor. TikTok's ordinary developer webhooks do not expose a general LIVE Gift event feed.
- **No TikTok video broadcast is included.** A browser can render the game, but sending the browser scene as a LIVE still requires TikTok-supported screen sharing, LIVE Studio, mobile screen broadcast, or a suitable authorized cloud encoder/RTMP access for the account. Do not assume RTMP privileges.
- The dashboard voice uses browser TTS when supported. OpenAI can generate dialogue if you configure `OPENAI_API_KEY` in Vercel, but the voice sound is the browser’s built-in speech engine. Playback may be restricted until a user clicks/taps inside the page; mobile background tabs may suspend it.
- This package includes no auto-gift prompting, prizes, chance games, fake viewers, or TikTok account scraping.

## No-install hosting with GitHub and Vercel
1. Unzip this package (Windows file explorer supports ZIP extraction). Go to [github.com/new](https://github.com/new) and create a **new** repository, e.g. `mochi-live-cloud` (do not overwrite your existing website repo).
2. Upload the contents of this folder to the repository root, maintaining the `api/` and `public/` directories. **Never upload .env or your secrets.**
3. Open [vercel.com/new](https://vercel.com/new), connect GitHub and import `mochi-live-cloud`. Keep framework preset **Other**; the `public/` static files and `api/` Node serverless functions are supported. Deploy.
4. The public game is `https://YOUR-PROJECT.vercel.app/`; dashboard at `https://YOUR-PROJECT.vercel.app/dashboard`.
5. Initially it's demo mode. For persistent cross-device updates, create Upstash Redis at [console.upstash.com](https://console.upstash.com/), then in Vercel Project → Settings → Environment Variables add `UPSTASH_REDIS_REST_URL`, `UPSTASH_REDIS_REST_TOKEN`, and `MOCHI_ADMIN_KEY` (random 32+ character secret). Optionally set `OPENAI_API_KEY` (paid OpenAI API account) to enable original AI dialogue and `OPENAI_MODEL` (defaults to `gpt-4o-mini`). Redeploy for env vars to take effect.
6. In the dashboard enter `MOCHI_ADMIN_KEY` and choose Save (kept in the tab's sessionStorage; don't share a screenshot that exposes it). Try the simulation controls. Open the game overlay on another device to verify updates.
7. To stream: use only TikTok LIVE broadcasting modes available to your account, e.g. an approved phone screen broadcast that captures the overlay, or a supported cloud encoder. Don't assume the Vercel URL itself streams video to TikTok.

## Later: actual LIVE gifts (not switched on)
- Verify an officially supported TikTok LIVE gifting integration for your account and region. The TikTok public developer Events webhook documentation does not list gift receipts. We are not using a hidden or unofficial TikTok credential scraper.
- A trusted third-party adapter can transform *legitimately received* events to POST `https://YOUR-PROJECT.vercel.app/api/event` with `X-Mochi-Key: <ADMIN_SECRET>` and JSON `{"type":"rose","user":"ViewerName","amount":1}`. Do not embed the key in public browser JS. Prefer a separate scoped ingestion key and rate limits before production use.
- The current event endpoint is an **integration contract and manual demo control**, not an already-integrated gift collector. Protect the token; rotate if leaked.

## Production hardening checklist
- For higher traffic, replace the simple Redis GET/SET pattern with atomic Lua scripts or Redis transactions to prevent lost updates from simultaneous events.
- Add rate limiting, a dedicated restricted ingest secret, event ID deduplication, and audit logging before enabling any real LIVE adapter.
- The `/dashboard` HTML is publicly reachable; the write operations require the administrator secret. If dashboard visibility is sensitive, add Vercel Authentication/SSO or an auth provider.
- Browser Web Speech API has browser/device limitations; don't treat it as server-generated AI audio.
- Moderate any future viewer-supplied text before creating spoken output.

## Account protection
Keep the on-screen 'AI CHARACTER' disclosure; use TikTok's AI content disclosure if applicable; avoid fake engagement, misleading gift pressure, gambling and unattended deceptive streams. Monitor the LIVE and review up-to-date TikTok account-specific rules.

## Endpoint examples
Read: `GET /api/state`
Write (admin/bridge only): `POST /api/event` JSON `{"type":"feed","user":"Friend","amount":1}` and `X-Mochi-Key` secret.
Valid type: `rose`, `gift`, `feed`, `play`, `rest`, `hello`, `reset`.
