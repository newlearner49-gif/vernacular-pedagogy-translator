# Vernacular Classroom Translator — Backend

Express + TypeScript API server that transcribes speech, generates
child-friendly pedagogical explanations across India's 22 scheduled
languages, and synthesizes speech for the results.

This is the backend half of the Vernacular Classroom Translator project.
The frontend lives in a separate repository:
`vernacular-translator-frontend`.

## Stack

- Express 5 (TypeScript, ESM)
- Zod-based request/response validation (`@workspace/api-zod`)
- Drizzle ORM + Postgres (`@workspace/db`)
- pino / pino-http logging
- esbuild for production bundling

## Project layout

```
.
├── src/
│   ├── app.ts              # Express app, middleware
│   ├── index.ts            # Entry point — starts the HTTP server
│   ├── lib/logger.ts        # pino logger
│   └── routes/              # /api/* route handlers
├── packages/
│   ├── api-zod/             # Shared request/response schemas & types
│   └── db/                  # Drizzle schema + Postgres client
├── openapi.yaml              # OpenAPI contract for the API
└── build.mjs                 # esbuild production build script
```

`packages/api-zod` and `packages/db` are local pnpm workspace packages —
they aren't published anywhere, they just keep the schema/DB code
separate from the HTTP layer.

## Requirements

- Node.js 20+
- pnpm 9+
- A Postgres database (if you use the `@workspace/db` package)

## Setup

```bash
pnpm install
cp .env.example .env
# then fill in .env with your own keys (see below)
```

### Environment variables

| Variable | Required | Description |
|---|---|---|
| `PORT` | no (defaults to `3001`) | Port the server listens on |
| `NODE_ENV` | no | `development` or `production` |
| `BHASHINI_API_KEY` | yes, for speech routes | Bhashini API key |
| `BHASHINI_USER_ID` | yes, for speech routes | Bhashini user ID |
| `GEMINI_API_KEY` | yes, for translation routes | Google Gemini API key |
| `SESSION_SECRET` | yes | Long random string used to sign sessions |
| `DATABASE_URL` | if using `@workspace/db` | Postgres connection string |
| `LOG_LEVEL` | no | pino log level, defaults to `info` |

Never commit your real `.env` file — only `.env.example` is tracked.

## Run locally

```bash
pnpm run dev
```

This type-checks, bundles with esbuild, and starts the server on
`http://localhost:3001` (or whatever `PORT` is set to).

## Build for production

```bash
pnpm run build
pnpm run start
```

`pnpm run build` type-checks the whole project and bundles `src/index.ts`
into `dist/index.mjs`. `pnpm run start` just runs that bundle.

## Database

If you're using the `@workspace/db` package, point `DATABASE_URL` at your
Postgres instance and push the schema:

```bash
pnpm run db:push
```

## API contract

`openapi.yaml` documents every route. The frontend's typed API client is
generated from this same contract, so keep them in sync if you change a
route's request or response shape.

## Deploying

This is a plain Node/Express app — it runs on any host that can run
`node dist/index.mjs` (Render, Railway, Fly.io, a VPS, etc.). Make sure
to set the environment variables above on whatever platform you deploy
to, and configure CORS/origin settings if the frontend is hosted on a
different domain.
