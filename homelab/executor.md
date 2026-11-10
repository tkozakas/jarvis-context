# Executor

`tkozakas/executor` is a small internal HTTP API on the host, the only way infra writes happen without the owner at a terminal. It keeps its own clone of the homelab repo, talks to the docker daemon through the mounted socket, and is reachable only on the `jarvis_default` docker network. jarvis calls it; nothing else does.

POST endpoints require `Authorization: Bearer $EXECUTOR_TOKEN`.

| Endpoint | Body | Effect |
|---|---|---|
| `GET /health` | — | Liveness |
| `POST /vault` | `{"updates": {"vault_key": "value"}}` | Decrypt `inventory/host_vars/nil/vault.yml`, add or replace keys, re-encrypt, commit and push |
| `POST /apply` | `{"stack": "name"}` | Render `<stack>.env.j2` from the vault, copy stack files to `/svc/<stack>/`, `docker compose --profile prod pull && up -d` |
| `POST /teardown` | `{"stack": "name"}` | `docker compose down --volumes --remove-orphans` and delete `/svc/<stack>/` |
| `POST /ansible/run` | `{"playbook": ..., "limit": ..., "tags": ...}`, all optional | Sync the homelab clone and run `ansible-playbook playbooks/<playbook>`; defaults to `raspberry-pi.yml` |

Constraints: vault keys must match `[a-z0-9_]+`, stack names `[a-z0-9-]+`, the playbook must exist under `playbooks/` in the clone. The vault password is written to `.vault_pass` for the run and removed afterwards.

It also drives Cloudflare for public subdomains — see [cloudflare.md](cloudflare.md).

Env vars: `EXECUTOR_TOKEN`, `VAULT_PASS`, `GH_TOKEN`, `GH_OWNER`, `CF_API_TOKEN`, `CF_ZONE`, `PORT`.

## From jarvis

The matching jarvis tools are `set_secrets`, `apply_stack`, `run_ansible`, `create_subdomain`, `remove_subdomain`. Every one of them asks for a confirmation button; none of them is auto-approved. A failed call captures the full executor output, retries infrastructure-shaped failures with backoff, and otherwise opens a bounded sandbox task on the relevant repo with that output before retrying.

A sandbox task cannot call the executor. Vault edits and ansible runs are owner steps from a task's point of view.
