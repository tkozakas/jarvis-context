# sea-battle

Public, two repos: `sea-battle-server` (Go, WebSockets, chi router) and `sea-battle-web` (vanilla HTML/CSS/JS, no build step). An online multiplayer battleship game.

## Stack

Deployed as the single `sea-battle` stack: `ghcr.io/tkozakas/sea-battle-server:main` on 8080, `ghcr.io/tkozakas/sea-battle-web:main` on 80, a per-stack Caddy routing by host, and a cloudflared tunnel.

- `sea-battle.tkozakas.com` → web
- `sea-battle-api.tkozakas.com` → server

Env: `TUNNEL_TOKEN`, `ALLOWED_ORIGINS`.

## Notes

- Two repos share one deploy hook (`deploy-sea-battle`); a push to either repo redeploys the whole stack.
- `ALLOWED_ORIGINS` on the server must match the web hostname or the WebSocket handshake is rejected.

## Known issues / CI state

Last checked 2026-11-10.

- Both repos green on `main`: `sea-battle-server` at `e9ecf2e`, `sea-battle-web` at `63e6e16`, both 2026-09-08.
- Two repos share one deploy hook, so the two halves can drift: pushing one redeploys the stack with the other at whatever it was.
- `ALLOWED_ORIGINS` on the server must match the web hostname; a mismatch breaks the WebSocket handshake at runtime without failing CI.
