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
