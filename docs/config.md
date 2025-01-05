# Basic Jujutsu configuration

Just like Git, Jujutsu has a configuration file that can be used to set up
defaults and aliases. The configuration file is located at
`~/.config/jj/config.toml`.

## Delta for pager

For massively better diff output for `jj diff` add this:

```toml
[ui]
# https://github.com/jj-vcs/jj/blob/v0.25.0/docs/config.md#processing-contents-to-be-paged
pager = "delta"

[ui.diff]
# NOTE: this is required when using `delta` as a pager
format = "git"
```

## Private Commits

Since Jujutsu automatically tracks basically *everything*, sometimes you want
to make sure you don't accidentally push a commit that contains sensitive
information. You can mark commits as private by adding a description that
starts with `private:`.

```toml
[git]
# https://jj-vcs.github.io/jj/latest/config/#set-of-private-commits
private-commits = 'description(glob:"private:*")'
```

## Better Describe Template

This adds a template for `jj describe`. It shows a basic summary of the added,
changed, deleted files as well as the full diff.

```toml
[templates]
# https://github.com/jj-vcs/jj/blob/v0.25.0/docs/config.md#default-description
draft_commit_description = '''
  concat(
    description,
    "\n",
    "JJ: This commit contains the following changes:\n",
    indent("JJ:    ", diff.summary()),
    "\n",
    "JJ: ignore-rest\n",
    diff.git(),
  )
'''
```

## Starship Integration

This was inspired by the [jj wiki](https://github.com/jj-vcs/jj/wiki/Starship)
entry for Starship config, but tweaked a bit. Ideally, Starship adds builtin
support for Jujutsu; some PR's & issues are open:

- https://github.com/starship/starship/pull/5772
- https://github.com/starship/starship/issues/5932
- https://github.com/starship/starship/issues/6076
- https://github.com/starship/starship/pull/6388

In the meantime, this works pretty well for me:

```toml
[custom.jj]
# combining `jj root` here to avoid having to execute `jj` multiple times
# this brings the total timings for `custom.jj` from 63ms down to 25ms
command = '''
jj root --ignore-working-copy > /dev/null 2>&1 && jj log --no-graph --color always --revisions @ --template '
  concat(
    "🍐",
    surround(
      "(",
      ")",
      separate(
        " ",
        change_id.shortest(8),
        commit_id.shortest(8),
        if(empty, label("empty", "(empty)")),
        if(description,
          concat("\"", description.first_line(), "\""),
          label(if(empty, "empty"), description_placeholder),
        ),
        bookmarks.join(", "),
        branches.join(", "),
        if(conflict, "💥"),
        if(divergent, "🚧"),
        if(hidden, "👻"),
        if(immutable, "🔒"),
      )
    )
  )
'
'''
# avoid running `jj root` both here and in the `command` above
when = true
```
