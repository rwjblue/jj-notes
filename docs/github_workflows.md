# GitHub Workflows

<!--toc:start-->
- [GitHub Workflows](#github-workflows)
  - [Setting up GH in non-colocated repos](#setting-up-gh-in-non-colocated-repos)
  - [Setting up a remote for a forked repo](#setting-up-a-remote-for-a-forked-repo)
<!--toc:end-->

## Setting up GH in non-colocated repos

If you are using "pure" `jj` (e.g. not using `jj git init --colocate` or `jj
clone --colocate`) then `gh` won't work out of the box because it can't find
the `.git` repo. You can fix that by setting up the `GIT_DIR` environment
variable.

```sh
export GIT_DIR="$PWD.jj/repo/store/git"
```

## Setting up a remote for a forked repo

Assuming you've configured `GIT_DIR`, you can use the `gh` CLI to create a fork:

```sh
gh repo fork
```

By default (without other args), this will change the original `origin` to be
named `upstream` and create a new remote named `origin` that points to your
fork. After this you have to fetch all remotes (or else you end up with an
error RE: `trunk()` alias being missing -- at least right now 2025-01-11):

```sh
jj git fetch --all-remotes
```

`jj` is already tracking `main@origin` (from our original `jj git clone`), but
that now points at our fork. We need instruct `jj` to track `main@upstream`
also now:

```sh
jj bookmark track main@upstream
```
