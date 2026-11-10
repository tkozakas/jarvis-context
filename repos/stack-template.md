# stack-template

Private. Minimal service templates for a containerized stack, one per language. Each is a working heartbeat service: reads `INTERVAL` (default 60s), prints a heartbeat, sleeps.

Templates: `templates/python` (uv, src layout, ruff, pytest), `templates/go` (flat module, table-driven tests, static binary), and a rust template.

## Notes

- Consumed by forge, not used directly.
- The conventions in [../conventions/coding.md](../conventions/coding.md) are what these templates already encode; a new service should not drift from them.
