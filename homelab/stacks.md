# Stacks on nil

The list and its order live in `tkozakas/homelab` → `inventory/host_vars/nil/vars.yml`. Each entry has a `name`, a compose `profile` (always `prod`), an optional `deploy: true` (gets a deploy webhook) and an optional `caddy: true` (gets a Caddyfile copied to the host).

| Stack | Deploy hook | Caddyfile | Images |
|---|---|---|---|
| `ingress` | no | yes | `caddy:alpine`, `almir/webhook:latest`, `cloudflare/cloudflared:latest` |
| `sea-battle` | yes | yes | `ghcr.io/tkozakas/sea-battle-server:main`, `ghcr.io/tkozakas/sea-battle-web:main`, caddy, cloudflared |
| `got` | yes | no | `ghcr.io/tkozakas/got:main`, `postgres:16-alpine`, `redis:alpine` |
| `gh-stats` | yes | yes | `ghcr.io/tkozakas/gh-stats-backend:main`, `ghcr.io/tkozakas/gh-stats-frontend:main`, caddy, cloudflared |
| `githuber` | yes | no | `ghcr.io/tkozakas/githuber:main` |
| `pimon` | yes | no | `ghcr.io/tkozakas/pimon:main` |
| `jarvis` | yes | no | `ghcr.io/tkozakas/jarvis:main`, `node:22-alpine` (copilot proxy), `tecnativa/docker-socket-proxy` |
| `executor` | yes | no | `ghcr.io/tkozakas/executor:main` |
| `streamtower` | yes | no | `ghcr.io/tkozakas/streamtower:main`, `ghcr.io/yourok/torrserver:latest`, `lscr.io/linuxserver/jackett:latest` |
| `hexwave` | yes | no | `ghcr.io/tkozakas/hexwave:main` |
| `wordforge` | yes | no | `ghcr.io/tkozakas/wordforge:main` |

## ingress

The entry point for everything public and for deploys.

- `caddy` listens on `:80` inside the `ingress` network and routes by path: `/hooks/*` to the webhook container, `/slack/*` to `githuber`, `/wordforge/*` to the wordforge container, everything else to `ghstats`.
- `webhook` (`almir/webhook`) serves the deploy hooks from a generated `hooks.json`, runs `deploy.sh` in the stack directory, and has the docker socket plus a static docker CLI mounted so it can pull and restart.
- `hooks-tunnel` is a cloudflared tunnel that exposes caddy publicly.

## jarvis

- `copilot` runs `copilot-api` as a local OpenAI-compatible endpoint on `:4141`; jarvis points `LLM_BASE_URL` at it.
- `jarvis` has the real docker socket mounted (it creates sandbox containers), a `state` volume at `/state`, and joins both `default` and `ingress`.
- `docker-proxy` is a read-only docker socket proxy (`POST=0`) for read paths.
- Sandbox containers are created on the `jarvis_default` network from `ghcr.io/tkozakas/jarvis-sandbox:main`.
- The `jarvis` stack is excluded from homelab's automatic stack redeploy (`NO_DEPLOY_STACK`); it deploys from its own repo's CI.

## executor

Single container, reachable only on `jarvis_default`. Mounts `/svc` (host stack directories), the docker socket, the ghcr auth dir and a `state` volume. See [executor.md](executor.md).

## Env templates

Every stack has `roles/raspberry-pi/templates/<stack>.env.j2` rendered from the vault into `<stack>/.env` with mode `0600`. Variable names only, grouped by stack:

| Stack | Env vars in the template |
|---|---|
| `ingress` | `HOOKS_TUNNEL_TOKEN`, `SVC_DIR`, `DOCKER_GID`, `DOCKER_AUTH`, `DEPLOY_TOKEN`, `DEPLOY_TOKEN_<STACK>` per deployable stack |
| `jarvis` | `TELEGRAM_BOT_TOKEN`, `TELEGRAM_CHAT_ID`, `GH_TOKEN`, `SANDBOX_GH_TOKEN`, `GHCR_TOKEN`, `GH_OWNER`, `DEPLOY_TOKEN`, `HOOKS_BASE`, `EXECUTOR_TOKEN` |
| `executor` | `EXECUTOR_TOKEN`, `VAULT_PASS`, `GH_TOKEN`, `GH_OWNER`, `CF_API_TOKEN`, `CF_ZONE` |
| `gh-stats` | `GITHUB_TOKEN`, `GITHUB_CLIENT_ID`, `GITHUB_CLIENT_SECRET`, `TUNNEL_TOKEN` |
| `sea-battle` | `TUNNEL_TOKEN`, `ALLOWED_ORIGINS` |
| `got` | `BOT_TOKEN`, `DB_URL`, `REDIS_ADDR` |
| `githuber` | `GITHUB_TOKEN`, `SLACK_BOT_TOKEN`, `SLACK_CHANNEL` |
| `pimon` | `TELEGRAM_BOT_TOKEN`, `TELEGRAM_CHAT_ID`, `HOST_LABEL`, `DEPLOY_TOKEN` |
| `streamtower` | `DATA_DIR`, `CACHE_DIR`, `META_DIR`, plus the optional tuning and auth vars listed in its compose file |
| `hexwave`, `wordforge` | none (static sites) |

Compose files also read optional vars with defaults; the template only has to carry the ones that come from the vault.

## Adding a stack

1. `roles/raspberry-pi/files/<name>/docker-compose.yml` with `profiles: ["prod"]` and the image from ghcr.
2. `roles/raspberry-pi/templates/<name>.env.j2` for its secrets, even if empty.
3. An entry in `inventory/host_vars/nil/vars.yml`.
4. Vault keys for the new `vault_*` names, and a `vault_deploy_token_<name>` if it gets a deploy hook — owner step.
5. An ansible run, which also regenerates `hooks.json` and registers the new webhook — owner or executor step.
