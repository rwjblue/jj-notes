# 🍐jj-notes

Personal documentation of common [Jujutsu (jj)](https://github.com/jj-vcs/jj)
version control workflows and commands for daily development tasks.

## Table of Contents

- [Configuration](./docs/config.md)

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
