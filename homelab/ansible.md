# Ansible

`tkozakas/homelab` is the single source of truth for what runs on the hosts. It is a plain ansible repo run with `ansible-playbook`, plus a `Taskfile.yml` of convenience targets.

## Playbooks

| Playbook | Target |
|---|---|
| `playbooks/site.yml` | Everything |
| `playbooks/raspberry-pi.yml` | `nil` only — the stacks host |
| `playbooks/router.yml` | `gl-mt6000` only |
| `playbooks/verify.yml` | Read-only router config checks |

Roles: `pi-base`, `raspberry-pi`, `tailscale` for the host; `router-base`, `router-dns`, `router-firewall`, `router-vpn`, `router-tailscale` for the router.

## What `raspberry-pi` does, in order

1. Read the docker group id; ensure the external `ingress` network exists.
2. Create `/svc/<stack>` for every stack in the list.
3. Copy each stack's `docker-compose.yml`, and its `Caddyfile` when `caddy: true`.
4. Install `deploy.sh` and render `hooks.json` from the stack list — this is what registers deploy webhooks.
5. Install a static docker CLI and compose plugin for the webhook container.
6. Install and cron the nightly backup and gc scripts.
7. Render `<stack>.env.j2` into `<stack>/.env` with mode `0600` (`diff: false`, so secrets never print).
8. Log in to ghcr if the stored credentials do not match the vault token.
9. `docker compose --profile prod up -d --remove-orphans` for every stack.

The run is idempotent and is the only thing that puts new files on the host.

## Secrets

Secrets live in `inventory/host_vars/<host>/vault.yml`, encrypted with ansible-vault, referenced as `vault_*` variables from the env templates. Editing: `task secrets` (`ansible-vault edit`). Agents cannot decrypt the vault; new keys are either an owner step or an executor `POST /vault` call.

## Taskfile targets

Configured by a local `.taskconfig` (see `.taskconfig.example`): `GH_OWNER`, `HOST_ADDR`, `HOST_USER`, `HOST_SSH_KEY`, `HOST_SVC_DIR`, `HOST_VARS`, `HOOKS_BASE`, `DEPLOY_TOKEN_VAULT_KEY`, `PLAYBOOK`.

| Target | What it does |
|---|---|
| `task deps` | Install the ansible collections from `requirements.yml` |
| `task check` | Syntax-check the playbook |
| `task diff` | `--check --diff` preview |
| `task deploy` | Run the playbook |
| `task secrets` | Edit the vault |
| `task status` | `docker ps` on the host |
| `task verify NAME=<stack>` | Compose ps and recent logs for one stack |
| `task logs NAME=<stack>` | Follow a stack's logs |
| `task ci NAME=<repo>` | Watch the latest CI run |
| `task redeploy NAME=<stack>` | Fire the stack's deploy webhook |

These are owner-side commands: they need SSH to the host and the vault password, neither of which a sandbox task has.
