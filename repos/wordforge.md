# wordforge

Private. A daily word puzzle in the browser: build words from seven letters, always using the centre one. The puzzle is derived from the date; progress and streaks live in `localStorage`. Vanilla ES modules, no backend, no runtime dependencies.

## Stack

Static site served by the container on port 80. `ghcr.io/tkozakas/wordforge:main`, deployed as the `wordforge` stack with the ingress alias `wordforge`.

No env vars.

## Notes

- Served publicly under a path, not a subdomain: the ingress Caddy does `handle_path /wordforge/*` → `wordforge:80`, with a redirect from `/wordforge`. Asset paths must stay relative or they break under that prefix.

## Known issues / CI state

Last checked 2026-11-10.

- **CI red on `main` since 2026-11-09** (latest `5d48432`); every recorded run for this repo has failed.
- Tests and both image builds pass; only `Trigger Deploy` fails, retrying the webhook ten times before giving up.
- The matching `homelab` run shows the cause: `wordforge: webhook returned 404`. The `deploy-wordforge` hook is not registered in the host's `hooks.json`, which only an ansible run fixes — an owner step.
- Consequence: the image is published but never deployed. Do not read a red run here as a code failure without checking which job failed.
- Served under a path prefix (`/wordforge/`), so absolute asset paths break at runtime without failing CI.
