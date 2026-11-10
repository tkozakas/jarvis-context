# Project notes

One file per project that matters to the homelab. Each covers what it is, its stack, anything about deploying it that is not the standard flow, and a **Known issues / CI state** section.

Not every repo under the owner account is here — only the ones that run on `nil`, that the deploy machinery depends on, or that agents use directly.

| File | Repo | Runs on nil | CI state (2026-11-10) |
|---|---|---|---|
| [jarvis.md](jarvis.md) | `tkozakas/jarvis` | yes | green |
| [executor.md](executor.md) | `tkozakas/executor` | yes | green |
| [pimon.md](pimon.md) | `tkozakas/pimon` | yes | green |
| [githuber.md](githuber.md) | `tkozakas/githuber` | yes | green |
| [got.md](got.md) | `tkozakas/got` | yes | green |
| [gh-stats.md](gh-stats.md) | `tkozakas/gh-stats` | yes | green |
| [sea-battle.md](sea-battle.md) | `tkozakas/sea-battle-server`, `tkozakas/sea-battle-web` | yes | green |
| [streamtower.md](streamtower.md) | `tkozakas/streamtower` | yes | green |
| [hexwave.md](hexwave.md) | `tkozakas/hexwave` | yes | **red** — deploy hook 404 |
| [wordforge.md](wordforge.md) | `tkozakas/wordforge` | yes | **red** — deploy hook 404 |
| [homelab.md](homelab.md) | `tkozakas/homelab` | no, it configures it | **red** — same deploy hook 404 |
| [forge.md](forge.md) | `tkozakas/forge` | no, scaffolding tool | green |
| [workflows.md](workflows.md) | `tkozakas/workflows` | no, shared CI | green (no real test suite) |
| [stack-template.md](stack-template.md) | `tkozakas/stack-template` | no, templates | green (no real test suite) |
| [devkit-mcp.md](devkit-mcp.md) | `tkozakas/devkit-mcp` | no, agent tooling | **red since 2026-05-27** |

The three deploy-hook failures are one problem with one fix: `wordforge` and `hexwave` have no registered webhook on the host because ansible has not run since they were added. See [homelab.md](homelab.md).

Keep the CI column and the per-file sections current — a stale "green" here is worse than no entry.
