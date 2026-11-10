# executor

Private. Internal HTTP API on `nil` that applies homelab changes: vault updates, stack applies and teardowns, ansible runs, and Cloudflare subdomain management.

## Stack

Python, uv, src layout, ruff + pytest. `ghcr.io/tkozakas/executor:main`.

Runs as the `executor` stack on the `jarvis_default` network only — not reachable from outside it. Mounts `/svc`, the docker socket, the ghcr auth dir and a `state` volume. Keeps its own clone of the homelab repo.

## Notes

- API surface and constraints: see [../homelab/executor.md](../homelab/executor.md).
- It holds the vault password (`VAULT_PASS`) and a write `GH_TOKEN`, so it is the highest-privilege service on the host. Changes here need care.
- Its only client is jarvis.

## Known issues / CI state

Last checked 2026-11-10.

- CI green on `main` (`d843f42`, 2026-09-08). The repo has been untouched since then.
- Several failures around 2026-09-06/07 preceded that commit; they were fixed, not carried.
- Standing risk rather than a bug: it holds `VAULT_PASS` and a write `GH_TOKEN`, so a mistake here is high blast radius.
