# devkit-mcp

Private. MCP server that helps coding agents with development and automation: parallel worktree tasks, pod script execution, and local-file pod monkeypatching. Used by agents directly; it does not run on `nil`.

## Stack

Rust, `cargo`, default branch `master` (not `main`). Installed locally with `cargo install --path .`, which puts `devkit-mcp` in `~/.cargo/bin`. There is also a `Dockerfile` and a `docker-compose.yml`, and CI publishes `ghcr.io/tkozakas/devkit-mcp` when the build gets that far.

Wired into opencode as a local MCP server; `opencode.json.example` has the config block.

## Tools it exposes

- `worktree.spawn_task`, `task_status`, `task_logs`, `task_wait`, `task_cleanup`, `worktree.list` — run shell commands in fresh worktrees as detached background tasks, in parallel.
- `k8s.run_script` — stage, copy and exec a script in a pod, resolving context, namespace and container.
- `rails.runner` — the same, for `rails runner`.
- `pod.monkeypatch`, `_revert`, `_reapply`, `_list` — patch a running pod with local files, with backup sessions.

## Known issues / CI state

Last checked 2026-11-10.

- **CI has been red on `master` since `b375a93` ("add smoke test script", 2026-05-27, ~167 days).** The last green run was `9843461` on 2026-05-16. The three pushes since — `b375a93`, `f1870c5`, `701395c` ("Add puma reload kind to pod.monkeypatch", 2026-07-09, ~124 days ago and still the head) — all failed.
- The failure moved: on `b375a93` only the `quality` job failed, at `cargo fmt --all -- --check`. On the current head `701395c` both `quality` and `cargo-deny` fail, so there is now an advisory, licence or bans finding on top of the formatting problem. `coverage` and `hadolint` stay green throughout.
- Because `docker` needs `quality`, `deny` and `hadolint`, it has been skipped on every run since May — **no image has been published for ~6 months**. `ghcr.io/tkozakas/devkit-mcp:latest` is stale; install from source instead.
- CI sets `RUSTFLAGS: "-D warnings"`, so any new warning is a hard failure. Expect a backlog of them after this long untouched.
- Treat a red run here as the normal state, not as evidence that your change broke something. Establish the baseline before and after.

Fixing it is roughly: `cargo fmt --all`, then `cargo clippy --all-targets --all-features -- -D warnings`, then `cargo deny check bans licenses sources advisories` locally, which will likely want `Cargo.lock` updates or `deny.toml` exceptions.
