# got

Public. Telegram bot written in Go.

## Stack

Go, with `Taskfile.yml`, `golangci-lint` and a `config.yaml` plus `translations/`. `ghcr.io/tkozakas/got:main`, deployed as the `got` stack alongside `postgres:16-alpine` and `redis:alpine`.

Env: `BOT_TOKEN`, `DB_URL`, `REDIS_ADDR`.

## Notes

- The only stack with its own database; the postgres and redis data live in stack volumes and are covered by the nightly backup job.
- TODO: what the bot actually does is not described in its README beyond "Telegram bot written in Go".
