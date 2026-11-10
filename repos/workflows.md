# workflows

Public. The reusable GitHub Actions workflow every service repo calls: builds a multi-arch docker image, pushes it to GHCR, and triggers the deploy webhook.

## Usage

Called as `tkozakas/workflows/.github/workflows/stack-pipeline.yml@main` with inputs like `context`, `dockerfile`, `image-suffix` and `build-args`, and the `deploy-token` / `deploy-url` secrets.

## Notes

- `image-suffix` is how one repo publishes a second image (jarvis uses it for `-sandbox`).
- Repo CI is normally: a `test` job the repo owns, then this pipeline gated on `github.event_name == 'push'`.
- Changing this workflow affects every service repo at once.
