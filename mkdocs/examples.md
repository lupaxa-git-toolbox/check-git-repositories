# Examples

Sample output and common scenarios for `check-git-repositories`.

## Typical listing

```text
Searching for Git repositories beneath: /Users/you/Desktop/GitMaster

[ OK ]       /Users/you/Desktop/GitMaster/Lupaxa/app
[ DIRTY ]    /Users/you/Desktop/GitMaster/Lupaxa/notes (2 files)
[ AHEAD ]    /Users/you/Desktop/GitMaster/Lupaxa/cli ↑1
[ NO-UP ]    /Users/you/Desktop/GitMaster/scratch/experiment (no upstream)

------------------------------------------------------------
Repositories checked: 4
OK:                   1
DIRTY:                1
AHEAD:                1
BEHIND:               0
DIVERGED:             0
NO-UP:                1
STASH:                0
ERROR:                0
```

Exit code for this run: `1`.

## Hide OK and NO-UP lines

```bash
check-git-repositories --ignore-clean --ignore-no-up ~/Desktop/GitMaster
```

Example list (summary still counts everything):

```text
[ DIRTY ]    /Users/you/Desktop/GitMaster/Lupaxa/notes (2 files)
[ AHEAD ]    /Users/you/Desktop/GitMaster/Lupaxa/cli ↑1

------------------------------------------------------------
Repositories checked: 4
OK:                   1
DIRTY:                1
AHEAD:                1
BEHIND:               0
DIVERGED:             0
NO-UP:                1
STASH:                0
ERROR:                0
```

With only dirty/ahead remaining as failures, exit is still `1`. If the tree
were only `OK` + `NO-UP`, exit would be `0` when `--ignore-no-up` is set.

## Verbose dirty detail

```bash
check-git-repositories -v ~/path/to/dirty-repo
```

```text
[ DIRTY ]    /path/to/dirty-repo (2 files) (no upstream)
       [unstaged:M] README.md
       [untracked]  scratch.tmp
```

## Diverged branch

```text
[ DIVERGED ] /path/to/repo ↑1 ↓2
```

Local has one unique commit; upstream has two the local branch lacks.

## Fetch before sync check

```bash
check-git-repositories --fetch ~/Desktop/GitMaster
```

!!! note "When to use --fetch"
    Use when you care about remote updates since your last fetch. Skip it for
    a fast local-only check of unpushed commits and dirty trees.

## Automation

Fail a shell script or CI step when anything still needs attention:

```bash
#!/usr/bin/env bash
set -euo pipefail
check-git-repositories --color=never --ignore-clean "$HOME/Desktop/GitMaster"
```

Pipe-friendly: `--color=never` (or a non-TTY pipe) keeps ANSI codes out of logs.

## Admonitions used in these docs

!!! tip "Tip"
    Prefer a fixed workspace root in an alias so the command is one word.

!!! warning "Warning"
    `--fetch` needs network access and may take a while on large trees.

!!! failure "Failure"
    Invalid flags or a missing `START_DIR` exit `2` and print a short error
    to stderr pointing at `--help`.
