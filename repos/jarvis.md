# jarvis

Private. Telegram bot that runs the infra cycle of `nil`: answers questions from live host and GitHub data, runs approved coding tasks in disposable sandbox containers, pushes them, watches CI and deploys, and watches the running services.

## Stack

Python, uv, src layout, ruff + pytest. Two uv projects in one repo: the bot at the root and the sandbox image under `sandbox/`. Packaged as `ghcr.io/tkozakas/jarvis:main` and `ghcr.io/tkozakas/jarvis-sandbox:main`.

Runs as the `jarvis` stack with a local `copilot-api` container as its OpenAI-compatible endpoint, a read-only docker socket proxy, the real docker socket for creating sandboxes, and a `/state` volume holding `state.json` and `stats.json`.

## Notes

- `AGENTS.md` and `README.md` in that repo are the detailed description of its behaviour; `docs/configuration.md` has the full env var list.
- Sandbox containers are created on `jarvis_default` and are long-lived per task id, reaped by idle TTL, max age and max live count.
- Infra writes go through the executor, always behind a confirmation button.
- CI runs lint and tests for both projects, then two stack-pipeline jobs: the bot image and the `-sandbox` image.
- The `jarvis` stack is excluded from homelab's automatic stack redeploy; it deploys from its own CI.
- Self-update is normal: jarvis can change and redeploy its own repo through the same pipeline.
