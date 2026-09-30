<p align="center">
    <a href="https://github.com/lupaxa-git-toolbox">
        <img src="https://raw.githubusercontent.com/the-lupaxa-project/brand-assets/master/logos/organisations/git-toolbox/readme-logo.png" alt="Git Toolbox" />
    </a>
</p>

<h1 align="center">Check Git Repositories</h1>

Scan a directory tree for git repositories and report anything that is not
**fully clean and synced with its upstream** — so uncommitted work, unpushed
commits, stashes, and sync drift are not overlooked at the end of the day.

A single portable bash script. Install it with Homebrew, or drop it in
`~/bin`. Requires only `bash`, `find`, and `git`.

## Quick Start

With Homebrew:

```bash
brew tap the-lupaxa-project/tap
brew trust the-lupaxa-project/tap
brew install check-git-repositories
```

Or copy the script onto your `PATH`:

```bash
mkdir -p ~/bin
cp src/check-git-repositories ~/bin/check-git-repositories
chmod +x ~/bin/check-git-repositories
export PATH="$HOME/bin:$PATH"

check-git-repositories --help
check-git-repositories ~/Desktop/GitMaster
```

From a clone, a symlink works the same way:

```bash
git clone git@github.com:lupaxa-git-toolbox/check-git-repositories.git
ln -s "$(pwd)/check-git-repositories/src/check-git-repositories" ~/bin/check-git-repositories
```

`START_DIR` defaults to `.` and must be an existing directory. The scan finds
every `.git` directory or file (including worktrees) and does not descend into
the gitdir. You get one status line per repository, then a count summary.

## What “OK” Means?

A repository is OK only when all of these hold:

- Working tree is clean (no staged, unstaged, untracked, or conflicted files)
- No stash entries
- Current branch has an upstream
- Branch is not ahead, behind, or diverged from that upstream

Anything else gets one primary status. The highest match wins:

| Prefix         | Meaning                                                           |
| :------------- | :---------------------------------------------------------------- |
| `[ ERROR ]`    | A git command failed (status, stash, rev-list, or optional fetch) |
| `[ DIRTY ]`    | Working tree has changes                                          |
| `[ DIVERGED ]` | Ahead and behind upstream                                         |
| `[ AHEAD ]`    | Local commits are not on upstream                                 |
| `[ BEHIND ]`   | Upstream has commits that are not local                           |
| `[ NO-UP ]`    | No upstream is configured (including many detached HEAD cases)    |
| `[ STASH ]`    | Clean and synced, but a stash entry exists                        |

Other issues can still appear as hints on that line, for example
`(3 files)`, `↑2`, `↓1`, `stash:1`, or `(no upstream)`.

Ahead and behind use local remote-tracking refs, so a normal scan does not
use the network. `--fetch` runs `git fetch --quiet` in each repo first. That
is slower, and a fetch failure is `[ ERROR ]` for that repo; the rest of the
scan continues.

## Options

| Option            | Description                                                                                             |
| :---------------- | :------------------------------------------------------------------------------------------------------ |
| `-v`, `--verbose` | Under dirty repos, print untracked, conflict, staged, and unstaged files                                |
| `--ignore-clean`  | Hide `[ OK ]` lines (still counted in the summary)                                                      |
| `--ignore-no-up`  | Hide `[ NO-UP ]` lines (still counted; those repos do not fail the exit code)                           |
| `--fetch`         | `git fetch --quiet` before ahead/behind checks                                                          |
| `--color=WHEN`    | `auto` (default), `always`, or `never`. `--color WHEN` is also accepted                                 |
| `-h`, `--help`    | Show usage and exit                                                                                     |
| `--`              | End of options                                                                                          |

Colour is on when stdout is a terminal. `--color=never` (or a pipe) keeps ANSI
codes out of logs. A non-empty `NO_COLOR` disables colour even with
`--color=always`.

When colour is on, `OK` is green, `DIRTY` and `STASH` are yellow, sync statuses
are cyan, and `ERROR` is red.

Ignore flags hide list lines only. Summary counts always reflect the true
status. The scan always finishes — it never stops on the first failure.

## Exit Codes

| Code | Meaning                                                                             |
| :--- | :---------------------------------------------------------------------------------- |
| `0`  | Every checked repo is `OK`, or only `OK` and `NO-UP` when `--ignore-no-up` is set   |
| `1`  | At least one repo needs attention (or errored)                                      |
| `2`  | Invalid CLI usage, including a bad flag or a `START_DIR` that is not a directory    |

Unknown options and invalid `--color` values exit `2` and print a short error
on stderr.

## Example

Show only repos that still need work, while the summary still counts everything:

```bash
check-git-repositories --ignore-clean ~/Desktop/GitMaster
echo "exit: $?"
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

Verbose detail for a dirty tree:

```text
[ DIRTY ]    /path/to/dirty-repo (2 files) (no upstream)
       [unstaged:M] README.md
       [untracked]  scratch.tmp
```

A diverged branch looks like `[ DIVERGED ] /path/to/repo ↑1 ↓2`.

Hide both clean and no-upstream lines when those repos are expected:

```bash
check-git-repositories --ignore-clean --ignore-no-up ~/Desktop/GitMaster
```

Fail a script when anything still needs attention:

```bash
check-git-repositories --color=never --ignore-clean "$HOME/Desktop/GitMaster"
```

A fixed root is easier as an alias:

```bash
alias gitcheck='check-git-repositories --ignore-clean ~/Desktop/GitMaster'
```

<a href="https://github.com/the-lupaxa-project">
    <img src="https://raw.githubusercontent.com/the-lupaxa-project/brand-assets/master/logos/components/footer-for-child-orgs.svg" alt="The Lupaxa Project Footer" width="100%" />
</a>
