# Check Git Repositories

A portable bash tool for scanning a directory tree of git repositories and
reporting anything that is not **fully clean and synced with its upstream**.

Use it at the end of the day (or anytime) so uncommitted work, unpushed
commits, stashes, missing upstreams, and sync drift are not overlooked.

## What it Checks?

A repository is **OK** only when all of these hold:

- Working tree is clean (no staged, unstaged, untracked, or conflicted files)
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

## Why Use It?

| Need                      | How this helps                                               |
| :------------------------ | :----------------------------------------------------------- |
| Many repos under one tree | Discovers every `.git` marker beneath a start directory      |
| End-of-day confidence     | Exit code `1` when anything still needs attention            |
| Scriptable                | Quiet colour when piped; exit `0` / `1` / `2` for automation |
| No install stack          | Single bash script — drop into `~/bin`                       |
