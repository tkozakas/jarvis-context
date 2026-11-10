# homelab

Public. The ansible repo that defines both hosts and every stack. Single source of truth for what runs where.

## Stack

Ansible (Jinja templates, `inventory/`, `playbooks/`, `roles/`), a `Taskfile.yml` of owner-side helpers, and an encrypted ansible-vault file per host.

## Notes

- Details: [../homelab/ansible.md](../homelab/ansible.md), [../homelab/stacks.md](../homelab/stacks.md), [../homelab/host.md](../homelab/host.md).
- Pushing to main validates every compose file and fires deploy webhooks for stacks whose files changed; `jarvis` is excluded and `[skip deploy]` in the commit message skips the job.
- Pushing does not copy new files to the host. Anything outside a stack directory — the stack list, env templates, `hooks.json`, cron scripts — needs an ansible run.
- Agents must keep diffs minimal here: edit only the lines the task requires, never regenerate a file wholesale, and always `git pull --rebase` first.
- Agents cannot decrypt or edit the vault.

## Known issues / CI state

Last checked 2026-11-10.

- **CI red on `main` since 2026-11-09** (latest `e03334a`), four consecutive failures after `dec3567` was green.
- `Validate` passes every time; only `Deploy changed stacks` fails, with `wordforge: webhook returned 404`.
- Root cause: the newer stacks (`wordforge`, `hexwave`) are in the stack list and their files are committed, but the host's generated `hooks.json` has not been regenerated, so their deploy hooks do not exist. The fix is an ansible run on `nil`, which agents cannot perform.
- Until that run happens, every push touching those stacks leaves this workflow red, and the same failure appears in the service repos' own pipelines.
- Expected trap: pushing here does not copy files to the host. A green `Validate` says nothing about the host being up to date.
