# got

Public. Telegram bot written in Go.

## Stack

Go, with `Taskfile.yml`, `golangci-lint` and a `config.yaml` plus `translations/`. `ghcr.io/tkozakas/got:main`, deployed as the `got` stack alongside `postgres:16-alpine` and `redis:alpine`.

Env: `BOT_TOKEN`, `DB_URL`, `REDIS_ADDR`.

## Notes

- The only stack with its own database; the postgres and redis data live in stack volumes and are covered by the nightly backup job.
- TODO: what the bot actually does is not described in its README beyond "Telegram bot written in Go".

## Known issues / CI state

Last checked 2026-11-10.

- CI green on `master` (`7100568`, 2026-09-07). Note the default branch is `master`, not `main`.
- No recurring issues recorded.
- TODO: the README does not say what the bot does, so behavioural changes have no written baseline to check against.
