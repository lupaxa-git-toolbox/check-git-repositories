# Reference

Command-line options, status labels, summary fields, and exit codes.

## Synopsis

```text
check-git-repositories [options] [START_DIR]
```

`START_DIR` defaults to `.` and must be an existing directory.

## Options

| Option            | Description                                                                                            |
| :---------------- | :----------------------------------------------------------------------------------------------------- |
| `-v`, `--verbose` | After each dirty repo line, print indented porcelain detail (untracked / conflict / staged / unstaged) |
| `--ignore-clean`  | Hide `[ OK ]` lines; still counted in the summary                                                      |
| `--ignore-no-up`  | Hide `[ NO-UP ]` lines; still counted; treated as acceptable for exit                                  |
| `--fetch`         | Run `git fetch --quiet` in each repo before ahead/behind checks                                        |
| `--color=WHEN`    | `auto` (default), `always`, or `never`. Also accepts `--color WHEN`                                    |
| `-h`, `--help`    | Print usage and exit `0`                                                                               |
| `--`              | End of options                                                                                         |

Unknown options and invalid `--color` values exit `2`.

Environment: if `NO_COLOR` is set (non-empty), colour is disabled even with
`--color=always`.

## Status labels

Each repository gets one **primary** status (highest applicable wins):

| Priority | Label      | Meaning                                                              |
| :------- | :--------- | :------------------------------------------------------------------- |
| 1        | `ERROR`    | Git command failed (status, stash list, rev-list, or optional fetch) |
| 2        | `DIRTY`    | Working tree has changes (porcelain non-empty)                       |
| 3        | `DIVERGED` | Ahead **and** behind upstream                                        |
| 4        | `AHEAD`    | Local commits not on upstream                                        |
| 5        | `BEHIND`   | Upstream has commits not in local                                    |
| 6        | `NO-UP`    | No upstream configured (including many detached HEAD cases)          |
| 7        | `STASH`    | Clean tree and synced, but stash entries exist                       |
| 8        | `OK`       | Clean, no stash, has upstream, fully synced                          |

Secondary issues may appear as hints on the same line, for example:

```text
[ DIRTY ]    /path/to/repo (3 files) ↑2 stash:1 (no upstream)
```

Tag formatting: `[ LABEL ]` with one space inside each bracket, right-padded
so paths align (width of `[ DIVERGED ]`).

## Colour (when enabled)

| Status                                 | Colour |
| :------------------------------------- | :----- |
| `OK`                                   | green  |
| `DIRTY`, `STASH`                       | yellow |
| `AHEAD`, `BEHIND`, `DIVERGED`, `NO-UP` | cyan   |
| `ERROR`, conflict detail               | red    |

## Summary

After the listing:

```text
------------------------------------------------------------
Repositories checked: N
OK:                   N
DIRTY:                N
AHEAD:                N
BEHIND:               N
DIVERGED:             N
NO-UP:                N
STASH:                N
ERROR:                N
```

Counts always sum to `Repositories checked` and always reflect true primary
statuses — ignore flags do not zero them out.

## Exit codes

| Code | When                                                                       |
| :--- | :------------------------------------------------------------------------- |
| `0`  | Every repo is `OK`, or `OK` plus only `NO-UP` when `--ignore-no-up` is set |
| `1`  | Any other non-OK status (including `ERROR`) remains                        |
| `2`  | Bad CLI usage or `START_DIR` is not a directory                            |

## Discovery

Repositories are found with `find` matching `.git` as a directory or file
(git worktrees), then pruned so the search does not descend into the gitdir.
