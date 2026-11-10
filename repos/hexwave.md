# hexwave

Private. A neon hex-grid arcade dodger that runs entirely in the browser. Vanilla JS, canvas, WebAudio-synthesised sound, no backend and no external assets.

## Stack

Static site served by the container on port 80. `ghcr.io/tkozakas/hexwave:main`, deployed as the `hexwave` stack with the container name and ingress alias `hexwave`.

No env vars.

## Notes

- Joins the `ingress` network with an alias, so a Cloudflare ingress rule can point at `http://hexwave:80`.
- TODO: its public hostname, if one is configured, is not recorded in any repo.

## Known issues / CI state

Last checked 2026-11-10.

- **CI red on `main` since 2026-11-09** (latest `2ffab6a`), four consecutive failing runs after `e88cb3b` was green.
- The failure is not in the code: lint, both arch builds and the manifest all pass, and only the `Trigger Deploy` job fails. It retries the deploy webhook ten times over ~2.5 minutes and gives up.
- Same symptom and same day as `wordforge` and the `homelab` stack deploy job, where the webhook answered 404 — the hook for these stacks is not registered on the host. Registering it needs an ansible run, which is an owner step.
- Consequence: the image is built and published, but the host is not restarted on push.
