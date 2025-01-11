# GitHub Workflows

## Setting up GH in non-colocated repos

If you are using "pure" `jj` (e.g. not using `jj git init --colocate` or `jj
clone --colocate`) then `gh` won't work out of the box because it can't find
the `.git` repo. You can fix that by setting up the `GIT_DIR` environment
variable.

```sh
export GIT_DIR="$PWD.jj/repo/store/git"
```
