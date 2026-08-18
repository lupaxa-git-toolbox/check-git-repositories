<p align="center">
    <a href="https://github.com/lupaxa-git-toolbox">
        <img src="https://raw.githubusercontent.com/the-lupaxa-project/brand-assets/master/logos/organisations/the-lupaxa-project/readme-logo.png" alt="The Lupaxa Project" />
    </a>
</p>

<h1 align="center">check-git-repositories</h1>

Scan a directory tree for git repositories and report anything that is not
**fully clean and synced with its upstream** — so uncommitted work, unpushed
commits, stashes, and sync drift are not overlooked at the end of the day.

A single portable bash script. Drop it in `~/bin`. Requires only `bash`,
`find`, and `git`.

## Quick start

```bash
mkdir -p ~/bin
cp src/check-git-repositories ~/bin/check-git-repositories
chmod +x ~/bin/check-git-repositories

check-git-repositories --help
check-git-repositories ~/Desktop/GitMaster
```

## What “OK” means

A repository is OK only when all of these hold:

- Working tree is clean
- No stash entries
- Current branch has an upstream
- Branch is not ahead, behind, or diverged from that upstream

Anything else is flagged, for example `[ DIRTY ]`, `[ AHEAD ]`, or `[ NO-UP ]`.

## Options

| Option            | Description                                              |
| :---------------- | :------------------------------------------------------- |
| `-v`, `--verbose` | Per-file dirty detail under dirty repos                  |
| `--ignore-clean`  | Hide `[ OK ]` lines (still counted in the summary)       |
| `--ignore-no-up`  | Hide `[ NO-UP ]` lines (still counted; ignored for exit) |
| `--fetch`         | `git fetch --quiet` before ahead/behind checks           |
| `--color=WHEN`    | `auto` (default), `always`, or `never`                   |
| `-h`, `--help`    | Show usage and exit                                      |

## Exit codes

| Code | Meaning                                        |
| :--- | :--------------------------------------------- |
| `0`  | Every checked repo is acceptable               |
| `1`  | At least one repo needs attention (or errored) |
| `2`  | Invalid CLI usage                              |

Ignore flags hide list lines only; summary counts always reflect the true
status. `--ignore-no-up` also treats `NO-UP` as acceptable for the exit code.
The scan always finishes before exiting — it never stops on the first failure.

## Example

```bash
check-git-repositories --ignore-clean ~/Desktop/GitMaster
```

```text
Searching for Git repositories beneath: /Users/you/Desktop/GitMaster

[ DIRTY ]    /Users/you/Desktop/GitMaster/notes (2 files)
[ AHEAD ]    /Users/you/Desktop/GitMaster/cli ↑1

------------------------------------------------------------
Repositories checked: 4
OK:                   2
DIRTY:                1
AHEAD:                1
BEHIND:               0
DIVERGED:             0
NO-UP:                0
STASH:                0
ERROR:                0
```

## Documentation

Full docs live under [`mkdocs/`](mkdocs/) and are built with MkDocs Material:

| Page                                         | Contents                      |
| :------------------------------------------- | :---------------------------- |
| [Getting started](mkdocs/getting-started.md) | Install and first run         |
| [Usage](mkdocs/usage.md)                     | Day-to-day workflows          |
| [Reference](mkdocs/reference.md)             | Options, statuses, exit codes |
| [Examples](mkdocs/examples.md)               | Sample output and scenarios   |

Serve locally:

```bash
python -m pip install -r requirements.txt
python -m mkdocs serve
```

Strict build:

```bash
python -m mkdocs build --strict
```

Published site (when enabled):
https://check-git-repositories.thelupaxaproject.org/

## Layout

```text
src/check-git-repositories   The tool
mkdocs/                      Documentation pages and assets
overrides/                   MkDocs Material theme overrides
mkdocs.yml                   Site configuration
requirements.txt             MkDocs dependencies
```

<a href="https://github.com/the-lupaxa-project">
    <img src="https://raw.githubusercontent.com/the-lupaxa-project/brand-assets/master/logos/components/footer-for-child-orgs.svg" alt="The Lupaxa Project Footer" width="100%" />
</a>
