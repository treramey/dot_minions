# dot_minions

Personal agent skill library and management script.

This repo stores local copies of agent skills and tracks which ones are active or shelved. The `./minions` script wraps common skill-management commands so skills can be installed, removed from the active set, and restored later without losing the local copy or source metadata.

## Contents

- `skills/` — local skill copies
- `.skill-lock.json` / `skills-lock.json` — source metadata for imported skills
- `shelf.json` — skills intentionally removed from the active set
- `minions` — CLI for listing, installing, shelving, activating, and formatting skills

## Requirements

- `git`
- `jq`
- `npx`
- `uvx` for Markdown formatting commands

The script uses the `skills` CLI through `npx`:

```bash
npx skills add ...
npx skills remove ...
```

## Quick start

```bash
git clone git@github.com:treramey/dot_minions.git
cd dot_minions

./minions census
./minions install
```

## Commands

```bash
./minions census
./minions activate <name>...
./minions kill <name>...
./minions install [repo]
./minions reset
./minions check [path]
./minions format [path]
./minions help
```

### `census`

Show active and shelved skills.

```bash
./minions census
```

### `activate`

Install one or more shelved skills back into the active set.

```bash
./minions activate prototype tdd
```

### `kill`

Remove one or more skills from the active set and add them to `shelf.json`.

```bash
./minions kill prototype
```

This does not delete the skill from the repo.

### `install`

Install skills from an upstream repo, then remove any skills listed in `shelf.json`.

```bash
./minions install
./minions install owner/repo
```

If no repo is provided, `minions` uses its default upstream repo.

### `reset`

Remove all installed skills and reset the shelf.

```bash
./minions reset
```

This requires confirmation.

### `check` and `format`

Check or format Markdown files with `mdformat` through `uvx`.

```bash
./minions check
./minions format
./minions check skills/tdd/SKILL.md
```

## Active vs shelved

A skill can be in one of two practical states:

- **active** — installed in the agent environment
- **shelved** — kept in the repo but removed from the active set

Shelved skills are recorded in `shelf.json`. Running `./minions install` re-applies that shelf after installing from upstream.

## License

MIT
