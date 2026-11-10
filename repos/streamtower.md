# streamtower

Private. Self-hosted torrent streaming server with instant playback: poster catalog, indexer search, and streaming straight to the browser from one origin. Sources come from Jackett with a public apibay fallback, playback is proxied from TorrServer, and files are transcoded only when the browser cannot play them directly.

## Stack

Go. `ghcr.io/tkozakas/streamtower:main`, deployed as the `streamtower` stack with `ghcr.io/yourok/torrserver` (8090) and `lscr.io/linuxserver/jackett` (9117). The player joins the `ingress` network with the alias `streamtower`.

Env: `DATA_DIR`, `CACHE_DIR`, `META_DIR` come from the vault template; the compose file reads a long list of optional tuning, auth and notification vars with defaults (`TS_AUTH_USER`/`TS_AUTH_PASS`, `STREAMTOWER_PASSWORD`, `SESSION_SECRET`, `JACKETT_*`, cache and rate limits, `V4L2_ENCODER` for hardware encoding).

## Notes

- By far the heaviest stack on the Pi: `GOMEMLIMIT` and `GOMAXPROCS` are pinned deliberately, and the cache directories live under `/srv/streamtower` on the host, not in a docker volume.
- Jackett's config file is bind-mounted read-only into the player so it can read the API key.
- The repo has its own `AGENTS.md` and `docs/`; read them before changing playback behaviour.
