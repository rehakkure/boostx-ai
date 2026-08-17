
## v1.3.1 Beta Hotfix

Fixed a backend `rateLimit is not defined` error that could block all generation requests. No AI generation flow or environment variables changed.
# BoostX.AI

BoostX.AI is a Telegram Mini App that turns a user's idea into a publish-ready social media post.

The project is intentionally small and production-oriented: Telegram authentication, one Node.js backend, OpenRouter, and a mobile-first interface.

## Current features

- Telegram Mini App
- Server-side Telegram `initData` validation
- AI generation through OpenRouter
- X / Twitter, LinkedIn, and Telegram formats
- Viral, Professional, and Casual tones
- Generate
- Regenerate
- Improve
- Copy Post
- Clear
- Mobile-first responsive UI
- Honest API and model errors with no fake fallback posts
- Output validation against reasoning/meta leakage
- Exact-character request handling with a dedicated free model
- Simple beta usage protection per Telegram user
- Health endpoint for deployment checks

## Architecture

```text
Telegram Bot
    ↓
Telegram Mini App
    ↓
Node.js HTTP server
    ↓
Telegram initData validation
    ↓
OpenRouter API
    ↓
AI model
    ↓
Mini App
```

## Project structure

```text
boostx-ai/
├── server.js
├── package.json
├── .gitignore
├── .env.example
├── README.md
└── public/
    └── index.html
```

## Requirements

- Node.js 20+
- Git
- GitHub account
- Render account
- Telegram bot created with BotFather
- OpenRouter API key

## Environment variables

Production values belong in Render Environment Variables. Never commit real secrets.

```text
BOT_TOKEN=
OPENROUTER_API_KEY=
OPENROUTER_MODEL=openrouter/free
EXACT_LENGTH_MODEL=openai/gpt-oss-120b:free
BETA_DAILY_LIMIT=15
```

### Notes

`OPENROUTER_MODEL` controls normal generation and defaults to `openrouter/free`.

`EXACT_LENGTH_MODEL` is used only when the user's request explicitly asks for a character count. It defaults to `openai/gpt-oss-120b:free`.

`BETA_DAILY_LIMIT` is an in-memory per-Telegram-user action limit for the private beta. The default is 15 actions per rolling 24-hour window. It resets when the service restarts because there is intentionally no database yet.

## Local development

Install dependencies:

```bash
npm install
```

Start the server:

```bash
npm start
```

The default local address is:

```text
http://localhost:10000
```

Health check:

```text
http://localhost:10000/api/health
```

A local browser can render the UI, but generation should be tested from Telegram because production requests require valid Telegram `initData`.

## Render deployment

Create a Render Web Service connected to the GitHub repository.

Build Command:

```text
npm install
```

Start Command:

```text
npm start
```

Add these Environment Variables:

```text
BOT_TOKEN
OPENROUTER_API_KEY
OPENROUTER_MODEL=openrouter/free
EXACT_LENGTH_MODEL=openai/gpt-oss-120b:free
BETA_DAILY_LIMIT=15
```

After deployment, verify:

```text
https://YOUR-RENDER-DOMAIN/api/health
```

The response should include:

```json
{
  "status": "ok",
  "server": "running",
  "aiKeyConfigured": true,
  "telegramConfigured": true,
  "model": "openrouter/free"
}
```

## Telegram setup

1. Create a bot with `@BotFather`.
2. Keep the Bot Token private.
3. Configure a Main Mini App or Menu Button.
4. Use the deployed Render HTTPS URL as the Mini App URL.
5. Open the bot in Telegram and launch BoostX.AI.

## OpenRouter setup

1. Create an OpenRouter API key.
2. Keep it private.
3. Add it to Render as `OPENROUTER_API_KEY`.
4. Keep `OPENROUTER_MODEL` configurable so the normal model can be changed without editing application code.

## API endpoint

### `GET /api/health`

Returns deployment and configuration status without exposing secrets.

### `POST /api/generate`

The Mini App uses the endpoint for three actions:

```json
{
  "action": "generate",
  "topic": "Post about cats",
  "platform": "X / Twitter",
  "tone": "Casual"
}
```

Regenerate includes the previous post so the backend can reject an exact duplicate.

Improve includes the current post and asks the model to improve hook, wording, readability, and structure while preserving the original subject.

## Output quality rules

The backend rejects:

- empty responses;
- accidental reasoning or prompt/meta commentary;
- obvious production notes;
- repeated Regenerate output;
- invalid exact-character results.

The system prompt also prevents the model from inventing the user's personal experiences, achievements, or identity.

### Exact character requests

Requests such as:

```text
a 300-character post about space
```

are detected by the backend. Those requests use the dedicated exact-length model and a maximum of two silent repair attempts. The backend validates the final character count before returning the result. If the model still cannot satisfy the exact count, the user receives an honest error instead of a fake or approximate result.

## Beta protection

To prevent one user from exhausting the free AI allowance during the private beta, the server limits each Telegram user to `BETA_DAILY_LIMIT` actions per rolling 24-hour window.

This is intentionally in-memory for the MVP and should be replaced by persistent usage tracking when a real public launch begins.

## Security

- API keys are never embedded in frontend code.
- Bot Token is never embedded in frontend code.
- Telegram `initData` is validated on the server.
- Logs never print secret values.
- User-controlled text is length-limited.
- No fake fallback AI content is used when generation fails.

## Product direction

The current version is beta-ready, not a full SaaS platform. The core flow is deliberately preserved so future releases can add persistent users, usage tracking, history, analytics, premium models, subscriptions, and payments without rewriting the Telegram → backend → OpenRouter foundation.
