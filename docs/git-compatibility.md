# Git compatibility

Jujutsu has two backends for storing commits. One of them uses a regular Git
repo, which means that you can collaborate with Git users without them even
knowing that you're not using the `git` CLI.

See `jj help git` for help about the `jj git` family of commands, and e.g.
`jj help git push` for help about a specific command (use `jj git push --help`
for more verbose help).


## Creating an empty repo
To create an empty repo using the Git backend, use `jj init --git <name>`. Since
the command creates a Jujutsu repo, it will have a `.jj/` directory. The
underlying Git repo will be inside of that directory (currently in
`.jj/repo/store/git/`).


## Creating a repo backed by an existing Git repo

To create a Jujutsu repo backed by a Git repo you already have on disk, use
`jj init --git-repo=<path to Git repo> <name>`. The repo will work similar to a
[Git worktree](https://git-scm.com/docs/git-worktree), meaning that the working
copies and the record of the current checkout will be separate, but the commits
will be accessible in both repos. Use `jj git import` to update the Jujutsu repo
with changes made in the Git repo. Use `jj git export` to update the Git repo
with changes made in the Jujutsu repo.

If you create initialize the Jujutsu repo in the same working copy as the Git
repo by running `jj init --git-repo=.`, then the import and export will happen
automatically on every command (because not doing that makes it very confusing
when the current checkout has changed in Git but not in Jujutsu or vice versa).
This mode is meant to make it easier to start using readonly `jj` commands in an
existing Git repo. You should then be able to switch to using mutating `jj`
commands and readonly Git commands. The mode is new and not tested much, and
interleaving mutating `jj` and `git` commands might not work well (feel free
to report bugs).


## Creating a repo by cloning a Git repo

To create a Jujutsu repo from a remote Git URL, use `jj git clone <URL>
[<destination>]`. For example, `jj git clone
https://github.com/octocat/Hello-World` will clone GitHub's "Hello-World" repo
into a directory by the same name.


## Branches

TODO: Describe how branches are mapped


## Format mapping details

Paths are assumed to be UTF-8. I have no current plans to support paths with
other encodings.

Commits created by `jj` have a ref starting with `refs/jj/` to prevent GC.

Commit metadata that cannot be represented in Git commits (such as the Change
ID) is stored outside of the Git repo (currently in `.jj/store/extra/`).

Paths with conflicts cannot be represented in Git. They appear as files with
a `.jjconflict` suffix in the Git repo. They contain a JSON representation with
information about the conflict. They are not meant to be human-readable.
