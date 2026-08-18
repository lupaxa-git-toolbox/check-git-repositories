# Usage

Day-to-day ways to run `check-git-repositories`.

## Basic scan

Scan the current directory tree:

```bash
check-git-repositories
```

Scan a specific root:

```bash
check-git-repositories ~/Desktop/GitMaster
```

## Compact listing vs verbose detail

By default each repo is one line. Add `-v` / `--verbose` to print per-file
dirty detail under repositories with a dirty working tree:

```bash
check-git-repositories -v ~/Desktop/GitMaster
```

## Hide noise with ignore flags

Ignore flags **hide list lines only**. Summary counts always reflect the true
status of every repository.

| Flag             | Effect                                                                                  |
| :--------------- | :-------------------------------------------------------------------------------------- |
| `--ignore-clean` | Hide `[ OK ]` lines; OK count still increments                                          |
| `--ignore-no-up` | Hide `[ NO-UP ]` lines; NO-UP count still increments; NO-UP does not fail the exit code |

Example — show only repos that still need work, while keeping accurate totals:

```bash
check-git-repositories --ignore-clean --ignore-no-up ~/Desktop/GitMaster
```

## Optional fetch

By default ahead/behind use local remote-tracking refs (fast, no network).
Pass `--fetch` to run `git fetch --quiet` in each repo before sync checks:

```bash
check-git-repositories --fetch ~/Desktop/GitMaster
```

!!! warning "Network and time"
    `--fetch` contacts remotes for every repository and is slower. Fetch
    failures are reported as `[ ERROR ]` for that repo; the scan continues.

## End-of-day checklist

1.   Run a full scan on your git workspace root.
2.   Fix or push anything flagged (`DIRTY`, `AHEAD`, `BEHIND`, `DIVERGED`, …).
3.   Re-run until the exit code is `0` (or only ignored `NO-UP` remaining when
     you intentionally use `--ignore-no-up`).

```bash
check-git-repositories --ignore-clean ~/Desktop/GitMaster
echo "exit: $?"
```

## Tips

!!! tip "Combine with shell aliases"
    Alias a habitual root and flags, for example:
    `alias gitcheck='check-git-repositories --ignore-clean ~/Desktop/GitMaster'`

!!! note "Full scan always completes"
    The tool never exits on the first problem repo. Every repository is
    checked and printed (unless hidden by an ignore flag) before the process
    exits with a status code.
