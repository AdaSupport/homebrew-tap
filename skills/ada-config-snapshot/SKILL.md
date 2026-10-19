---
name: ada-config-snapshot
description: Version-control an Ada agent's configuration and promote changes between instances using adacli snapshots (export → git → diff → import). Use when the user wants to back up, diff, restore, GitOps, or copy config from one Ada instance to another (e.g. staging → production).
require-tools:
  - Bash
  - Read
  - Write
---

# Ada config as version-controlled snapshots

`adacli snapshot` exports an instance's full configuration to one deterministic
(key-sorted, diffable) JSON file, and imports it back — the basis for GitOps and
promotion between instances.

## Back up / baseline

```bash
adacli <instance> snapshot export -o <instance>.json
git add <instance>.json && git commit -m "baseline config for <instance>"
```

Filter resource types with `--include` / `--exclude` (e.g. `--include knowledge guidance`).

API traffic profiles are opt-in and not part of a plain export or import. To carry them,
for example into a clone used for simulations, pass `--with-profiles` to both
`snapshot export` and `snapshot import`, and keep `actions` in any `--include`. It needs
an Ada employee login and a sampler-enabled bot. A profile holds values seen in the
source bot's production responses, and the plain export does not scrub them, so do not
commit or share a `--with-profiles` export.

## See what changed

After edits in the dashboard (or before importing), diff the live instance against a file:

```bash
adacli <instance> snapshot diff <instance>.json
```

Show the user the diff and get agreement before any import.

## Promote between instances (e.g. staging → prod)

Always dry-run first, then apply:

```bash
# Merge: add/update resources from the file, leave others intact
adacli <target> snapshot import staging.json --mode merge --dry-run
adacli <target> snapshot import staging.json --mode merge

# Replace: DESTRUCTIVE — deletes everything not in the file. Requires --confirm.
adacli <target> snapshot import staging.json --mode replace --dry-run
# ...only after explicit user confirmation:
adacli <target> snapshot import staging.json --mode replace --confirm
```

## Rules

- **Never `--mode replace` without a dry-run + explicit user sign-off** — it deletes.
- Respect read_only instances; if the target is read_only the import is refused.
- Commit the snapshot before and after a change so the repo is the source of truth.
- Use `--include`/`--exclude` to keep promotions surgical (e.g. promote only `playbooks`).
- Leave out `--preserve-ids` between Ada instances. Another bot's IDs would move that bot's documents, so the importer refuses the flag unless the target is the `test` bot of a local stack. A restore of the same bot keeps its IDs anyway.
