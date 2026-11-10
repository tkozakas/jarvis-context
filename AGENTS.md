# Agent context

This repo is a knowledge base, not a service. It holds what an agent needs to know about the homelab host, the projects that run on it, and the conventions they follow. Nothing here is executable; nothing here is a source of truth for configuration.

## Source of truth

Facts here are derived from the real repos and may drift. When a decision depends on a precise value, read the real thing:

| Question | Authoritative source |
|---|---|
| What runs on the host, with what image and env | `tkozakas/homelab` → `roles/raspberry-pi/files/<stack>/docker-compose.yml` and `roles/raspberry-pi/templates/<stack>.env.j2` |
| Which stacks exist and in what order they start | `tkozakas/homelab` → `inventory/host_vars/nil/vars.yml` |
| Secret values | the ansible vault in `tkozakas/homelab`, encrypted, applied by the owner |
| What jarvis can do | `tkozakas/jarvis` → `README.md`, `AGENTS.md`, `docs/configuration.md` |
| What the executor exposes | `tkozakas/executor` → `README.md` |

If this repo disagrees with one of those, the real repo wins, and the file here should be corrected.

## Layout

```
AGENTS.md            this file: entry point
README.md            pointer to this file
homelab/             the host, its stacks, deploy flow, executor, ansible, public hostnames
repos/               one file per owner project: purpose, stack, deploy notes
conventions/         coding, commit and deploy conventions that apply across repos
```

- [homelab/host.md](homelab/host.md) — hosts, layout on disk, networks, cron jobs
- [homelab/stacks.md](homelab/stacks.md) — every stack, its images and its env template
- [homelab/deploy-flow.md](homelab/deploy-flow.md) — push → CI → webhook → restart
- [homelab/executor.md](homelab/executor.md) — the only path for infra writes
- [homelab/ansible.md](homelab/ansible.md) — the playbook and what it does on the host
- [homelab/cloudflare.md](homelab/cloudflare.md) — tunnels, ingress rules, public subdomains
- [repos/](repos/) — per-project notes
- [conventions/coding.md](conventions/coding.md), [conventions/commits.md](conventions/commits.md), [conventions/deploy.md](conventions/deploy.md)

## How to use this

1. Read this file first, then the one or two files that cover the area you are changing. Do not read everything.
2. Before acting on a fact from here, confirm it against the authoritative source above if the action is destructive or deploys something.
3. If you learn something durable that is not written down, add it to the right file in the same commit as the work.
4. Anything not verifiable from the repos is marked `TODO`. Do not replace a `TODO` with a guess — either verify it or leave it.

## Hard boundaries

- The host filesystem is not writable from a task. Changes to what runs reach the host only through the homelab repo plus an ansible run, or through the executor.
- Sandbox tasks commit on `main` and push directly. No branches, no pull requests.
- Vault edits and ansible runs are owner or executor actions; a sandbox task cannot do them and must name them as a remaining owner step.
