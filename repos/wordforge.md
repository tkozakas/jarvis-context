# wordforge

Private. A daily word puzzle in the browser: build words from seven letters, always using the centre one. The puzzle is derived from the date; progress and streaks live in `localStorage`. Vanilla ES modules, no backend, no runtime dependencies.

## Stack

Static site served by the container on port 80. `ghcr.io/tkozakas/wordforge:main`, deployed as the `wordforge` stack with the ingress alias `wordforge`.

No env vars.

## Notes

- Served publicly under a path, not a subdomain: the ingress Caddy does `handle_path /wordforge/*` → `wordforge:80`, with a redirect from `/wordforge`. Asset paths must stay relative or they break under that prefix.
