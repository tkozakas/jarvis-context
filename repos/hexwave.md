# hexwave

Private. A neon hex-grid arcade dodger that runs entirely in the browser. Vanilla JS, canvas, WebAudio-synthesised sound, no backend and no external assets.

## Stack

Static site served by the container on port 80. `ghcr.io/tkozakas/hexwave:main`, deployed as the `hexwave` stack with the container name and ingress alias `hexwave`.

No env vars.

## Notes

- Joins the `ingress` network with an alias, so a Cloudflare ingress rule can point at `http://hexwave:80`.
- TODO: its public hostname, if one is configured, is not recorded in any repo.
