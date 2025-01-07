# 🍐jj-notes

Personal documentation of common [Jujutsu (jj)](https://github.com/jj-vcs/jj)
version control workflows and commands for daily development tasks.

## Table of Contents

- [Configuration](./docs/config.md)
- [Clone a Repository](#cloning-a-repository)
  - [Cloning with Jujutsu (Non-colocated)](#cloning-with-jujutsu-non-colocated)
  - [Setting up Colocated Repo](#setting-up-colocated-repo)
- [Making changes](#making-changes)
- [Pushing changes directly to a tracked branch](#pushing-changes-directly-to-a-tracked-branch)
- [Pulling updates from the remote](#pulling-updates-from-the-remote)
- [Updating local changes on top of a remote branch](#updating-local-changes-on-top-of-a-remote-branch)
- [Start adding changes to a remote branch](#start-adding-changes-to-a-remote-branch)
- [See a complete diff between two branches](#see-a-complete-diff-between-two-branches)
- [See a GitHub Pull Request style diff](#see-a-github-pull-request-style-diff)

## Guides

### Cloning a Repository

There are two main ways to clone a repo:

- Using a colocated git repo (where `.git` is in the repo root)
- Using git as a backing store, but not colocated (`.git` ends up in
  `.jj/repo/store/git`)

#### Cloning with Jujutsu (Non-colocated)

In this setup, there is still a backing `git` repo but it isn't top level. This
means other tooling doesn't think it's in a `git` repo (e.g. `starship` prompt
won't include "detached head" in the prompt output all the time).

```sh
jj git clone https://github.com/rwjblue/jj-notes.git
cd jj-notes/
```

#### Setting up Colocated Repo

This keeps a top level `.git` folder, and allows you to always "drop back" to
using `git` commands if you need to. This exposes the ultimate escape hatch,
but is sometimes annoying because most git tooling assumes that a "detached
head" state is a bad thing (or otherwise makes it annoying).

```sh
cd some-repo/
jj git init --colocate
```

### Making changes

In general, you want to end up with an empty commit at the end of your changes
(also true after a fresh clone). So let's assume that is where you are starting
from.

This is one of the places where `jj` really shines. You basically just make the
changes you want and the next time that `jj` runs (or automatically if you have
the `watchman` integration enabled) it will automatically snapshot the changes
into the current change.

Then at any point, you can describe the set of changes you are doing:

```sh
jj describe
```

Then type up your commit message. I use the same basic format as I would in
`git` (a shorter first line, then a longer description).

When you are done with this change, you can use `jj new` to create a new empty
commit and leave the working directory in a clean state.

```sh
jj new
```

### Pushing changes directly to a tracked branch

I use this workflow a lot for things like
[rwjblue/dotfiles](https://github.com/rwjblue/dotfiles) and
[rwjblue/dotvim](https://github.com/rwjblue/dotvim) where I mostly just push
directly to `master` | `main` branches. In this case, I'm not really worrying
about collaborating with others (these are just personal repos after all), but
the same workflow can be used for shared branches (and `jj` does a great job at
helping you avoid mutating commits that have already been pushed).

Let's assume you just finished [making a change](#making-changes) and you are
ready to push them:

```sh
jj bookmark move main --to @-
```

This updates the [bookmark](https://jj-vcs.github.io/jj/latest/bookmarks/)
(which is basically Jujutsu's branch concept) to point to the change just
before the current working commit (sorta akin to `HEAD^` in git, but in `jj`
you always have a change for the working directory instead of the index).

Now that the bookmark is updated, you can push the changes:

```sh
jj git push
```

### Pulling updates from the remote

```sh
jj git import
```

This does not update the local working copy (unlike something like `git pull`).

### Updating local changes on top of a remote branch

After [pulling updates from the remote](#pulling-updates-from-the-remote), you
might want to update your working copy to the latest changes from `main` (or
other tracked branch):

```sh
jj rebase --destination main
```

This will rebase the current revision (e.g. `@`) and any of it's ancestors that
aren't already part of `main` on top of the latest `main` revision.

### Start adding changes to a remote branch

Make sure that you have the latest changes:

```sh
jj git import
```

Then you can start making changes:

```sh
jj new <branch-name>@<remote-name>
```

### See a complete diff between two branches

If you want to see a literal diff of the changes between two branches (e.g.
from your local branch and `main`), you would do this:

```sh
jj diff --from main
```

This will show additions in your branch, but also any changes added to `main`
as deletions (because those changes are not in your branch).

### See a GitHub Pull Request style diff

More often, you want to see the changes that you've introduced in a branch
since the branching point (e.g. ignore any new additions to `main`). This is
effectively a GitHub Pull Request style diff.

```sh
jj diff --from 'fork_point(main|@)'
```
