# workflows

Public. The reusable GitHub Actions workflow every service repo calls: builds a multi-arch docker image, pushes it to GHCR, and triggers the deploy webhook.

## Usage

Called as `tkozakas/workflows/.github/workflows/stack-pipeline.yml@main` with inputs like `context`, `dockerfile`, `image-suffix` and `build-args`, and the `deploy-token` / `deploy-url` secrets.

## Notes

- `image-suffix` is how one repo publishes a second image (jarvis uses it for `-sandbox`).
- Repo CI is normally: a `test` job the repo owns, then this pipeline gated on `github.event_name == 'push'`.
- Changing this workflow affects every service repo at once.

## Known issues / CI state

Last checked 2026-11-10.

- Only a Dependency Graph workflow runs here, last green (`a8ccbe5`, 2026-09-01). There is no test suite for the reusable workflow itself.
- Its `Trigger Deploy` step retries the webhook ten times at 15s intervals and then fails the run. A missing or unregistered hook therefore surfaces as a ~2.5 minute red job in the *calling* repo, with the build itself green — the current `wordforge` and `hexwave` failures.
- Changes here hit every service repo at once and nothing catches a regression before they do.
