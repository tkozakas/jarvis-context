# Deploy conventions

## Changing an existing service

Push to its repo on `main`. CI builds the image and fires the deploy hook; the host pulls and restarts. Nothing else is needed as long as the compose file, env template and stack entry already exist.

Manual retrigger:

```sh
curl -X POST -H "Authorization: $DEPLOY_TOKEN" "$HOOKS_BASE/deploy-<stack>"
```

## Changing what a service needs from the host

A new env var, a new volume, a new port or a new image name is a change to `tkozakas/homelab`:

- new or changed env var → `roles/raspberry-pi/templates/<stack>.env.j2` plus a vault key
- anything else → `roles/raspberry-pi/files/<stack>/docker-compose.yml`

A push to homelab redeploys stacks whose files changed, but only an ansible run copies new or changed env templates and the stack list to the host.

## Adding a new service

1. Scaffold with forge: `SCAFFOLD_DEST=/workspace python3 forge/scaffold.py <name> <python|go|rust>`. This creates the private repo, its CI and its deploy secrets.
2. Write the service and its tests, push to `main`, and get CI green so `ghcr.io/tkozakas/<name>:main` exists.
3. Wire it into homelab: `roles/raspberry-pi/files/<name>/docker-compose.yml` with `profiles: ["prod"]` and the ghcr image, `roles/raspberry-pi/templates/<name>.env.j2`, and an entry in `inventory/host_vars/nil/vars.yml`. Push homelab `main`.
4. Owner steps, which an agent cannot do: add the `vault_*` keys including `vault_deploy_token_<name>`, and run the playbook. The ansible run is what registers the new deploy webhook; before it, CI's deploy call for that stack fails.

Always name the remaining owner steps explicitly in the task report.

## Exposing something publicly

Public access is a Cloudflare tunnel ingress rule, created through the executor's subdomain tools, not by opening a port. The container must join the `ingress` network with a stable alias so the rule can target `http://<alias>:<port>`. See [../homelab/cloudflare.md](../homelab/cloudflare.md).

## Rules

- Never `ssh` into the host to change things by hand; the host state must stay reproducible from the homelab repo.
- Never restart or redeploy a container directly from a task. Push, or use the deploy hook.
- A deploy is not done when the container starts. It is done when CI is green, the hook returned 2xx and the service answers.
