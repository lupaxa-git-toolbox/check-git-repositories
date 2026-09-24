<p align="center">
    <a href="https://github.com/lupaxa-git-toolbox">
        <img src="https://raw.githubusercontent.com/the-lupaxa-project/brand-assets/master/logos/organisations/git-toolbox/readme-logo.png" alt="Git Toolbox" />
    </a>
</p>

<h1 align="center">Check Git Repositories</h1>

Scan a directory tree for git repositories and report anything that is not
**fully clean and synced with its upstream** — so uncommitted work, unpushed
commits, stashes, and sync drift are not overlooked at the end of the day.

A single portable bash script. Drop it in `~/bin`. Requires only `bash`,
`find`, and `git`.

## Quick Start

```bash
mkdir -p ~/bin
cp src/check-git-repositories ~/bin/check-git-repositories
chmod +x ~/bin/check-git-repositories

check-git-repositories --help
check-git-repositories ~/Desktop/GitMaster
```

## What “OK” Means?

A repository is OK only when all of these hold:

- Working tree is clean
- No stash entries
- Current branch has an upstream
- Branch is not ahead, behind, or diverged from that upstream

Anything else is flagged with one of these prefixes:

| Prefix         | Meaning                                    |
| :------------- | :----------------------------------------- |
| `[ ERROR ]`    | A git command failed                       |
| `[ DIRTY ]`    | Working tree has changes                   |
| `[ DIVERGED ]` | Ahead and behind upstream                  |
| `[ AHEAD ]`    | Local commits are not on upstream          |
| `[ BEHIND ]`   | Upstream has commits that are not local    |
| `[ NO-UP ]`    | No upstream is configured                  |
| `[ STASH ]`    | Clean and synced, but a stash entry exists |

## Options

| Option            | Description                                              |
| :---------------- | :------------------------------------------------------- |
| `-v`, `--verbose` | Per-file dirty detail under dirty repos                  |
| `--ignore-clean`  | Hide `[ OK ]` lines (still counted in the summary)       |
| `--ignore-no-up`  | Hide `[ NO-UP ]` lines (still counted; ignored for exit) |
| `--fetch`         | `git fetch --quiet` before ahead/behind checks           |
| `--color=WHEN`    | `auto` (default), `always`, or `never`                   |
| `-h`, `--help`    | Show usage and exit                                      |

## Exit Codes

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

Install, usage, reference, and examples:

<https://check-git-repositories.thelupaxaproject.org/>

Site pages live in `mkdocs/`. Serve them from this checkout:

```bash
python -m pip install -r requirements.txt
mkdocs serve
```

After `make update`, `make mkdocs-serve` does the same.

<a href="https://github.com/the-lupaxa-project">
    <img src="https://raw.githubusercontent.com/the-lupaxa-project/brand-assets/master/logos/components/footer-for-child-orgs.svg" alt="The Lupaxa Project Footer" width="100%" />
</a>
