# forge

Private. Scaffolds a new service repo from `stack-template`: copies the template for a language, renames the placeholder, and publishes a private GitHub repo with CI and deploy secrets already set.

## Stack

Python, uv. Not deployed; it is run from inside sandbox containers, where it is cloned to `./forge`.

## Usage

```sh
SCAFFOLD_DEST=/workspace python3 forge/scaffold.py <name> <python|go|rust>
```

`--dry-run` skips publishing. Env: `GH_OWNER`, `HOOKS_BASE`, `DEPLOY_TOKEN`, `SCAFFOLD_DEST`, `TEMPLATE_REPO`.

## Notes

- Scaffolding only creates the repo. Wiring the service into the homelab repo and running ansible are separate steps — see [../conventions/deploy.md](../conventions/deploy.md).
