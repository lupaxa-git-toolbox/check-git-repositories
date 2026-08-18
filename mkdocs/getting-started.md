# Getting started

Install the script somewhere on your `PATH`, then point it at a directory that
contains one or more git repositories.

## Requirements

- bash
- `find`
- `git`

No Python, Node, or other runtime is required to **run** the tool. Python is
only needed if you build or serve these docs locally.

## Install

Clone the repository (or copy the script) and place it on your `PATH`:

=== "Copy into ~/bin"

    ```bash
    mkdir -p ~/bin
    cp src/check-git-repositories ~/bin/check-git-repositories
    chmod +x ~/bin/check-git-repositories
    # Ensure ~/bin is on PATH
    export PATH="$HOME/bin:$PATH"
    ```

=== "Symlink from a clone"

    ```bash
    git clone git@github.com:lupaxa-git-toolbox/check-git-repositories.git
    ln -s "$(pwd)/check-git-repositories/src/check-git-repositories" ~/bin/check-git-repositories
    chmod +x ~/bin/check-git-repositories
    ```

=== "Run from the repo"

    ```bash
    ./src/check-git-repositories --help
    ```

## First run

```bash
check-git-repositories --help
check-git-repositories ~/Desktop/GitMaster
```

You should see a banner for the start directory, one status line per
repository, then a summary of counts for every status.

## Colour

Colour is **auto** by default: enabled when stdout is a terminal, disabled when
piped. Override with `--color=always` or `--color=never`. If `NO_COLOR` is set
in the environment, colour stays off.

## Exit codes

| Code | Meaning                                             |
| :--- | :-------------------------------------------------- |
| `0`  | Every checked repo is acceptable.                   |
| `1`  | At least one repo needs attention (or errored).     |
| `2`  | Invalid CLI usage (bad flag, missing directory, …). |

See [Reference](reference.md) for the full status model and options.
