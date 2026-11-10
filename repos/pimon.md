# pimon

Private. Telegram infra dashboard for the homelab: host stats, container health, network reachability, AdGuard DNS stats, alerts and admin commands. One status message edited in place on an interval, plus alert messages when something breaks.

## Stack

Python, uv, src layout. `ghcr.io/tkozakas/pimon:main`, deployed as the `pimon` stack. Env: `TELEGRAM_BOT_TOKEN`, `TELEGRAM_CHAT_ID`, `HOST_LABEL`, `INTERVAL`, `DEPLOY_TOKEN`.

## Notes

- Overlaps with jarvis's watchdog; pimon is the passive dashboard, jarvis is the one that acts.
- It has a `DEPLOY_TOKEN`, so it can fire deploy webhooks from its admin commands.
