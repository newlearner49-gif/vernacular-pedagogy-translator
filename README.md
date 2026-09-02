# Vernacular Classroom Translator — Frontend

A React + Vite classroom tool that converts typed or spoken concepts
into child-friendly explanations across all 22 scheduled Indian
languages, with speech transcription and speech synthesis built in.

This is the frontend half of the Vernacular Classroom Translator
project. The API server lives in a separate repository:
`vernacular-translator-backend`. You'll need it running (locally or
deployed) for this app to actually work.

## Stack

- React 19 + Vite 7 (TypeScript)
- Tailwind CSS 4
- shadcn/ui + Radix UI primitives
- TanStack Query
- wouter (routing)
- A typed API client generated from the backend's OpenAPI spec (`@workspace/api-client-react`)

## Project layout

```
.
├── src/
│   ├── App.tsx               # Main app — recorder, language picker, results
│   ├── main.tsx               # Entry point
│   ├── components/            # UI components (shadcn/ui + app-specific)
│   ├── hooks/
│   ├── lib/utils.ts
│   └── pages/not-found.tsx
├── packages/
│   └── api-client-react/       # Generated, typed fetch + React Query client
├── public/
├── index.html
└── vite.config.ts
```

`packages/api-client-react` is a local pnpm workspace package generated
from the backend's OpenAPI contract — it isn't published anywhere, it's
just kept separate from the app code.

## Requirements

- Node.js 20+
- pnpm 9+
- The backend running somewhere reachable (see the backend repo's README)

## Setup

```bash
pnpm install
cp .env.example .env
```

### Environment variables

| Variable | Required | Description |
|---|---|---|
| `PORT` | no (defaults to `5173`) | Dev server port |
| `BASE_PATH` | no (defaults to `/`) | Base path the app is served from |
| `VITE_API_BASE_URL` | no in dev, yes in production | Backend URL. In dev, the Vite server proxies `/api/*` requests to this URL. In a production build, the compiled app calls this URL directly. Leave unset in production only if the frontend and backend are served from the exact same origin. |

## Run locally

Make sure the backend is running first (defaults to `http://localhost:3001`
— see the backend repo), then:

```bash
pnpm run dev
```

Open `http://localhost:5173`. API calls to `/api/*` are proxied to the
backend automatically in dev, so no extra CORS setup is needed locally.

## Build for production

```bash
pnpm run build
```

Output goes to `dist/`. Set `VITE_API_BASE_URL` to your deployed
backend's URL before building (e.g. `VITE_API_BASE_URL=https://api.example.com pnpm run build`),
unless the frontend will be served from the same origin as the API.

Preview the production build locally with:

```bash
pnpm run serve
```

## Deploying

`dist/` is a static site — it can be hosted on Vercel, Netlify,
Cloudflare Pages, GitHub Pages, or any static file host. Just make sure
`VITE_API_BASE_URL` was set correctly at build time, and that the
backend's CORS configuration allows requests from wherever this app
ends up hosted.
