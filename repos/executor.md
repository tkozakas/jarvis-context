# executor

Private. Internal HTTP API on `nil` that applies homelab changes: vault updates, stack applies and teardowns, ansible runs, and Cloudflare subdomain management.

## Stack

Python, uv, src layout, ruff + pytest. `ghcr.io/tkozakas/executor:main`.

Runs as the `executor` stack on the `jarvis_default` network only — not reachable from outside it. Mounts `/svc`, the docker socket, the ghcr auth dir and a `state` volume. Keeps its own clone of the homelab repo.

## Notes

- API surface and constraints: see [../homelab/executor.md](../homelab/executor.md).
- It holds the vault password (`VAULT_PASS`) and a write `GH_TOKEN`, so it is the highest-privilege service on the host. Changes here need care.
- Its only client is jarvis.
