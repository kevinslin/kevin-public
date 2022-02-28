---
id: s6yZ7v5dny6WPjx9s8Qtn
title: Git
desc: ''
updated: 1645073238344
created: 1645060259804
---


## Aliases

```conf
[alias]
    # list files which have changed since REVIEW_BASE
    # (REVIEW_BASE defaults to 'master' in my zshrc)
    files = !git diff --name-only \"$REVIEW_BASE\"
    # Same as above, but with a diff stat instead of just names
    # (better for interactive use)
    stat = !git diff --stat $(git merge-base HEAD \"$REVIEW_BASE\")


    # --- Commit
    # quickly commit everything
    c = "!git add -A && git commit -m"
    co = !git commit --message
    # quickly reset everything
    wipe = !git reset --hard && git clean -f
    # continue from rebase
    rcon = !git rebase --continue

    # --- Meta
    # update config
    econfig = !vim ~/.gitconfig

    # --- New
    # 2022-02-16
    # fetch all and rebase
    f = !git fetch --all && git rebase origin/master

    # ---- Other
    diffNoVersion = !git diff \"$REVIEW_BASE\" -- . ':(exclude)**/CHANGELOG.md' ':(exclude)**/package.json' ':(exclude)CHANGELOG.md'
    amend = !git commit --amend
    track = !git checkout --track 

```
