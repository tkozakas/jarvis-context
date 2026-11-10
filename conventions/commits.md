# Commit and branching conventions

- **Work on `main`.** Never create a branch or a pull request for agent work.
- **Push directly.** CI on `main` runs the checks, builds the image and redeploys.
- **Title only.** A commit message is one imperative line with no body, no bullet list, no "Co-authored-by" and no AI attribution.
- **One change per commit.** Keep the diff to what the task required.
- **`git pull --rebase` before committing** in any repo another actor also writes to — in practice always for `homelab`, which the executor pushes to as well.
- **Minimal diffs in config repos.** Edit only the lines the task needs; never regenerate or reformat a whole file.
- **Never commit secrets.** Secrets go in the ansible vault, referenced by `vault_*` name.

Examples of acceptable titles:

```
Rewrite README and drop the improvement backlog
Route plain follow-up messages by intent instead of a button
Add wordforge stack to nil
```

## Commit message switches

`[skip deploy]` anywhere in a `homelab` commit message skips that push's stack redeploy job. Nothing else is interpreted.
