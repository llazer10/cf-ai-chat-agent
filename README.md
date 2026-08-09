# Game Recommender Agent

A conversational video-game recommender built on Cloudflare Workers AI and the Agents SDK. It streams responses from `@cf/zai-org/glm-4.7-flash`, can query RAWG for current game records, and offers built-in recommendations by genre, PC performance, or play style.

## What it does

- Searches RAWG by genre and optional platform, returning up to five highly rated results
- Maps common phrases such as “open world” and “sci-fi” to RAWG genre slugs
- Provides built-in recommendation tools for genre/platform, low- or high-end PCs, and five play-style categories
- Streams chat responses through an `AIChatAgent`
- Stores each agent instance in a SQLite-backed Durable Object configured as `ChatAgent`
- Supports adding and removing MCP servers, including the OAuth callback flow exposed by the Agents SDK
- Includes a React/Kumo chat UI with light/dark themes, connection state, tool output, reasoning panels, debug JSON, stop, and clear-history controls

## Architecture

```text
React + Kumo UI
      │
      │ Agents SDK WebSocket/chat protocol
      ▼
ChatAgent (AIChatAgent / Durable Object)
      ├─ Workers AI: @cf/zai-org/glm-4.7-flash
      ├─ RAWG API tool
      ├─ local recommendation tools
      └─ tools from connected MCP servers
```

`routeAgentRequest()` handles Agents SDK and OAuth requests. Cloudflare's asset configuration serves the single-page app and runs the Worker first for `/agents/*` and `/oauth/*`.

## Prerequisites

- Node.js and npm
- A Cloudflare account with Wrangler authenticated
- A [RAWG](https://rawg.io/apidocs) API key for `searchRealGames`

## Local development

```bash
git clone https://github.com/llazer10/cf_ai_game_agent.git
cd cf_ai_game_agent
npm install
npx wrangler login
```

Create a local `.dev.vars` file (it is ignored by Git):

```dotenv
RAWG_API_KEY=your_rawg_api_key
```

Then start the Vite/Workers development server:

```bash
npm run dev
```

Open the local URL printed by Vite (normally `http://localhost:5173`). The Workers AI binding is configured with `remote: true`, so local AI inference uses the remote Cloudflare binding and requires Cloudflare authentication/network access.

Example prompts:

- `Recommend highly rated RPGs for PC.`
- `What should I play on a low-end PC?`
- `I like relaxed games—what would fit me?`

## Useful scripts

| Command | Purpose |
| --- | --- |
| `npm run dev` | Start local development |
| `npm run check` | Check formatting, lint TypeScript, and type-check |
| `npm run types` | Regenerate Wrangler binding types |
| `npm run deploy` | Build the frontend and deploy with Wrangler |

## Deploy

Authenticate Wrangler, configure the production RAWG secret, and deploy:

```bash
npx wrangler login
npx wrangler secret put RAWG_API_KEY
npm run deploy
```

`wrangler.jsonc` defines the Worker name, Workers AI binding, static assets, `ChatAgent` Durable Object, SQLite migration, and observability. Deployment requires a Cloudflare account configured for these resources. This repository alone does not establish that a production deployment is currently live.

## Security

Keep `RAWG_API_KEY` out of source control. Use `.dev.vars` locally and a Wrangler secret for deployed environments; do not place the key in `wrangler.jsonc` or client-side code.

## License

MIT. See [`LICENSE`](LICENSE). The current license notice is Copyright © 2025 Cloudflare Inc.
