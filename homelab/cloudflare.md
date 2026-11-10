# Cloudflare tunnels and public hostnames

Public traffic reaches the host only through Cloudflare tunnels; no ports are forwarded. Each publicly exposed stack runs its own `cloudflare/cloudflared` container with `command: tunnel run` and a `TUNNEL_TOKEN` from the vault, so a tunnel's config lives in Cloudflare, not in the repo.

Known tunnel containers: `ingress` (`HOOKS_TUNNEL_TOKEN`), `gh-stats` (`TUNNEL_TOKEN`), `sea-battle` (`TUNNEL_TOKEN`).

## Zone and hostnames

The zone is `tkozakas.online` (executor env `CF_ZONE`). Hostnames confirmed from the repos:

| Hostname | Serves |
|---|---|
| `tkozakas.online` | The ingress Caddy: `/hooks/*` → deploy webhooks, `/slack/*` → githuber, `/wordforge/*` → wordforge, everything else → gh-stats |
| `sea-battle.tkozakas.online` | sea-battle web client |
| `sea-battle-api.tkozakas.online` | sea-battle Go server |
| `ghstats.fun` | gh-stats, its own domain, used in `GITHUB_REDIRECT_URL`, `FRONTEND_URL`, `CORS_ORIGINS` |

TODO: the full ingress rule set lives in the Cloudflare tunnel configs and is not mirrored in any repo; `hexwave` and `streamtower` join the `ingress` network with aliases but their public hostnames, if any, are not recorded here.

## Managed subdomains

The executor creates and removes subdomains through the Cloudflare API, exposed to jarvis as `create_subdomain` and `remove_subdomain`. For a create it resolves the zone, picks the tunnel (by name, or the one the apex CNAME points at, or the single active one), upserts an ingress rule mapping the hostname to a service target, and ensures the `<id>.cfargotunnel.com` CNAME.

The service target is a URL reachable from the tunnel container, normally a container alias on the `ingress` network, for example `http://hexwave:80`.

After the records exist, jarvis polls the new public URL with DNS-propagation-aware backoff for roughly three minutes and only reports it created once a probe comes back non-5xx; a timeout is reported as unverified and fed into the remediation loop.

Both operations always ask the owner for confirmation.
