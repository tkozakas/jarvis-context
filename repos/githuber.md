# githuber

Public. Telegram/Slack bot for open GitHub PRs: one live card per PR, updated in place and removed on merge or close. Notifies on CI going green, new comments and reviews.

## Stack

Python, uv, src layout. `ghcr.io/tkozakas/githuber:main`, deployed as the `githuber` stack. Env: `GITHUB_TOKEN`, `SLACK_BOT_TOKEN`, `SLACK_CHANNEL`.

## Notes

- Publicly reachable at `tkozakas.com/slack/*`, routed by the ingress Caddy to the container on port 8080 — that path is the Slack callback endpoint.

## Known issues / CI state

Last checked 2026-11-10.

- CI green on `main` (`202dd96`, 2026-10-25).
- No recurring issues recorded.
