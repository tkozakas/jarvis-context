# gh-stats

Public. GitHub analytics dashboard with contribution streaks, language stats and country rankings. Lives at [ghstats.fun](https://ghstats.fun).

## Stack

Monorepo: TypeScript frontend (`frontend/`) and a backend (`backend/`), each published as its own image — `ghcr.io/tkozakas/gh-stats-backend:main` and `ghcr.io/tkozakas/gh-stats-frontend:main`.

Deployed as the `gh-stats` stack: backend on 8080, frontend on 3000, a per-stack Caddy routing `/api/*` to the backend and everything else to the frontend, and a cloudflared tunnel.

Env: `GITHUB_TOKEN`, `GITHUB_CLIENT_ID`, `GITHUB_CLIENT_SECRET`, `TUNNEL_TOKEN`, plus `GITHUB_REDIRECT_URL`, `FRONTEND_URL` and `CORS_ORIGINS` which default to the `ghstats.fun` origin.

## Notes

- It is also the ingress Caddy's default backend, so `tkozakas.online` with no matching path serves gh-stats.
- Two images from one repo means a change to either side triggers the same CI; the deploy hook is a single `deploy-gh-stats`.
