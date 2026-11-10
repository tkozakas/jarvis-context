# Coding conventions

These apply to every owner project unless the repo's own `AGENTS.md` says otherwise.

- **No code comments.** The code explains itself; if it cannot, restructure it.
- **Config from env vars only.** No config files baked into images, no hardcoded hosts or tokens.
- **Constants over magic values.** Name the number at the top of the module.
- **Small single-purpose modules.** One responsibility per file; prefer a new module over growing an existing one.
- **Tests for behavior, not plumbing.** Test what the code does, not that a function calls another function.
- **No unnecessary dependencies.** Prefer the standard library. A new dependency needs a reason.

## Per language

| Language | Layout and tooling |
|---|---|
| Python | uv project, `src/<name>/` layout, `ruff check` and `ruff format --check`, `pytest tests` |
| Go | flat module with `cmd/` and `internal/`, table-driven tests, static binary, `golangci-lint` where configured |
| JS | vanilla ES modules where possible, `eslint.config.js`, no runtime dependencies for static sites |

Standard commands in a Python repo:

```sh
uv sync
uv run --group dev ruff check .
uv run --group dev ruff format --check .
uv run --group dev pytest tests
```

## Documentation

- The repo `README.md` describes what the thing is, how it runs and its env var names — never aspirational features or roadmaps.
- A repo `AGENTS.md` holds the behavioural detail an agent needs. Keep it current with the code in the same commit.
- Durable facts about the host or another project belong in this context repo, not scattered in service READMEs.
