# dot_minions

Personal agent skill **store** and a symlink manager that fans it out to the
harnesses that do not read the store directly.

This repo is the single home for every skill — your own plus vendored
third-party ones. `skills/` is the store and `.skill-lock.json` /
`skills-lock.json` are the provenance locks (managed by `npx skills`). The
`./minion` script keeps the store fresh and **symlinks** each active skill into
each symlink harness, so every tool sees the same live copy and edits stay in
sync. Codex and Pi read `~/.agents/skills` directly, so they do not need
symlinks under their own config directories.

## Model

- **Store** — `skills/` + the global lock file, here in `~/.agents`. Adding and
  updating skills is `npx skills`' job, but it must be run with `--global` so
  the CLI targets `~/.agents/skills` instead of project-local `.agents/skills`.
- **Symlink harnesses** — `minion` symlinks active store skills into:
  - `~/.claude/skills` (Claude Code)
- **Direct readers** — Codex and Pi read `~/.agents/skills` directly.
- **Symlinks are absolute** (`~/.agents/skills/<name>` → `<harness>/<name>`).
  `minion` only ever touches entries whose name matches a store skill — foreign
  skills and plain files are left untouched. Legacy Codex/Pi symlinks that point
  back into the store are pruned during sync/reset.

## Contents

- `skills/` — the store (one dir per skill, each with a `SKILL.md`)
- `.skill-lock.json` / `skills-lock.json` — source/provenance locks (the CLI's)
- `shelf.json` — skills intentionally unlinked from the symlink harnesses
- `minion` — CLI for syncing the store out to the symlink harnesses

## Quick start

```bash
git clone git@github.com:treramey/dot_minions.git .agents
cd .agents

./minion install
```

## Commands

```bash
./minion census
./minion install
./minion add <repo> [skill...]
./minion activate <name>...
./minion kill <name>...
./minion reset
./minion check [path]
./minion format [path]
./minion help
```

### `census`

Show active skills (with how many of the symlink harnesses currently hold a
live symlink, e.g. `1/1`) and shelved skills.

```bash
./minion census
```

### `install`

Refresh the global store via `npx skills update --global` (best-effort —
per-source failures are logged, not fatal), then symlink every active
(non-shelved) store skill into every symlink harness and prune any dead,
shelved, or legacy Codex/Pi links.

```bash
./minion install
```

Requires a clean git tree to start. Re-running is idempotent: links already
correct are left as-is, and a manually deleted link is recreated.

### `add`

Add skills from a repo to the global store + lock via `npx skills add --global`,
then run the symlink fan-out so they land in every symlink harness in one step.

```bash
./minion add treramey/skills            # all skills in the repo
./minion add mattpocock/skills diagnose # specific skills
```

(You can still call `npx skills add … --global` directly; `add` just chains the
symlink step.)

### `activate`

Unshelve one or more store skills and symlink them back into every symlink
harness.

```bash
./minion activate prototype tdd
```

### `kill`

Drop a skill's symlinks from every symlink harness and record it in
`shelf.json`. The store copy is kept.

```bash
./minion kill prototype
```

### `reset`

Unlink every store skill from all symlink harnesses and reset the shelf. Does
**not** delete the store or the locks.

```bash
./minion reset
```

This requires confirmation.

### `check` and `format`

Check or format Markdown files with `mdformat` through `uvx`.

```bash
./minion check
./minion format
./minion check skills/tdd/SKILL.md
```

## Active vs shelved

- **active** — installed in the agent environment
- **shelved** — kept in the repo but removed from the active set

Shelved skills are recorded in `shelf.json`. `./minion install` skips shelved
skills when linking and prunes any of their stale links.

## License

MIT
