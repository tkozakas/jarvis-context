# Hosts

Two managed hosts, both in `tkozakas/homelab` → `inventory/hosts.yml`.

| Host | Group | Role |
|---|---|---|
| `nil` | `raspberry_pis` | Runs every docker stack and the deploy webhook receiver |
| `gl-mt6000` | `routers` | OpenWrt router: WAN, DNS, VPN, firewall, Tailscale |

Addresses, users and passwords come from the encrypted vault (`vault_pi_host`, `vault_pi_user`, `vault_pi_password`, `vault_router_user`, `vault_router_password`).

## nil

Architecture is ARM (Raspberry Pi); images are built multi-arch by the shared pipeline.

Layout on disk:

```
/home/<pi_user>/svc/<stack>/docker-compose.yml   copied by ansible from the homelab repo
/home/<pi_user>/svc/<stack>/.env                 rendered by ansible from <stack>.env.j2 + vault
/home/<pi_user>/svc/<stack>/Caddyfile            only for stacks marked caddy: true
/home/<pi_user>/svc/ingress/deploy/              deploy.sh, hooks.json, static docker + compose binaries
/home/<pi_user>/backups/                         nightly volume backups, backup.log, gc.log
/home/<pi_user>/.docker/config.json              ghcr pull credentials
```

Every stack is started with `docker compose --profile prod up -d --remove-orphans`, so every service in a compose file carries `profiles: ["prod"]`.

### Networks

- `default` — per-stack bridge network created by compose.
- `ingress` — external docker network shared by stacks that are reachable from the outside; the ingress Caddy and the per-stack Caddy/app containers join it with aliases.
- `jarvis_default` — the jarvis stack's own network; the executor joins it so jarvis can reach it and nothing else can.

### Scheduled host jobs

| When | What |
|---|---|
| 03:30 daily | `/usr/local/bin/homelab-backup` — volume backups into `~/backups` |
| 04:30 daily | `/usr/local/bin/homelab-gc` — docker image garbage collection |

Both are cron entries owned by the pi user and log into `~/backups/`.

### Logging

Every compose file uses the same `json-file` driver with `max-size: 10m`, `max-file: 3`. Older container logs are therefore gone; do not assume long history is retrievable.

## gl-mt6000

OpenWrt, configured over SSH with `raw`/`uci` tasks. DNS chain:

```
clients -> dnsmasq(:53) -> AdGuard Home(:3053) -> dnsforge.de over DoH/DoT
VPN clients -> wgclient1 dnsmasq -> stubby(:5453) -> dnsforge.de over DoT
```

Roles: `router-base`, `router-dns`, `router-firewall`, `router-vpn`, `router-tailscale`. Timezone is `Europe/Vilnius`.

TODO: WireGuard peer inventory and firewall rule intent are only in the role templates; no higher-level description exists yet.
