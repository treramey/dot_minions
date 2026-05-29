# dot_minions

Personal agent skill library and management script.

This repo stores local copies of agent skills and tracks which ones are active or shelved. The `./minion` script wraps common skill-management commands so skills can be installed, removed from the active set, and restored later without losing the local copy or source metadata.

## Contents

- `skills/` — local skill copies
- `.skill-lock.json` / `skills-lock.json` — source metadata for imported skills
- `shelf.json` — skills intentionally removed from the active set
- `minion` — CLI for listing, installing, shelving, activating, and formatting skills


## Quick start

```bash
git clone git@github.com:treramey/dot_minions.git .agents
cd .agents

./minion install
```

## Commands

```bash
./minion census
./minion activate <name>...
./minion kill <name>...
./minion install [repo]
./minion reset
./minion check [path]
./minion format [path]
./minion help
```

### `census`

Show active and shelved skills.

```bash
./minion census
```

### `activate`

Install one or more shelved skills back into the active set.

```bash
./minion activate prototype tdd
```

### `kill`

Remove one or more skills from the active set and add them to `shelf.json`.

```bash
./minion kill prototype
```

This does not delete the skill from the repo.

### `install`

Install skills from an upstream repo, then remove any skills listed in `shelf.json`.

```bash
./minion install
./minion install owner/repo
```

If no repo is provided, `minion` uses its default upstream repo.

### `reset`

Remove all installed skills and reset the shelf.

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

A skill can be in one of two practical states:

- **active** — installed in the agent environment
- **shelved** — kept in the repo but removed from the active set

Shelved skills are recorded in `shelf.json`. Running `./minion install` re-applies that shelf after installing from upstream.

## License

MIT
