---
id: fnw2ms7nw007dzibz0rdnyu
title: Shortcuts
desc: ''
updated: 1666021643604
created: 1648916969485
---

## Modifiers
- ctrl
- [lr]shift
- [lr]alt
- [lr]cmd

## Overview
- navigation
- views: eg. switch views
- execute
    - execute-cell:
        - ctrl-enter: execute cell (default)
        - shift-space: my override [2023-02-24 08:53]
    - execute and enter below 
        - shift-enter: default
    - execute-cell and insert below:
        - alt-enter: edfault

- launch
    - bring up alfred

## Global
- launch:
    - alfred: ctrl-space
    - raycast: alt-space

## VSCode

### Toggle Side Bar
- shortcut: cmd+b
- default: true

### Toggle Secondary Side Bar
- shortcut: cmd+ctrl+b
- default: false
- start: 2022.04.02

### Search Editor

```json
    // ---> Search
    // Look across all schemas
    {
        "key": "ctrl+s m",
        "command": "search.action.openNewEditor",
        "args": {
            "query": "",
            "triggerSearch": false,
            "focusResults": false,
            "includes": "*.schema.yml"
        }
    },
    // Open search editor at current hierarchy
    {
        "key": "ctrl+s f",
        "command": "search.action.openNewEditor",
        "args": {
            "query": ".",
            "triggerSearch": true,
            "focusResults": false,
            "includes": "${fileBasenameNoExtension}*",
            "regexp": true,
            "showIncludesExcludes": true
        }
    },
    // look for tags
    {
        "key": "ctrl+s t",
        "command": "search.action.openNewEditor",
        "args": {
            "query": "#\\w",
            "triggerSearch": true,
            "focusResults": false,
            "includes": "${fileBasenameNoExtension}*",
            "regexp": true,
            "showIncludesExcludes": true,
            "contextLines": 0
        }
    },
    // look at backlinks
    {
        "key": "ctrl+s b",
        "command": "search.action.openNewEditor",
        "args": {
            "query": "\\[\\[.*${fileBasenameNoExtension}(?!\\.)",
            "isCaseSensitive": true,
            "isRegexp": true,
            "matchWholeWord": true,
            "triggerSearch": true,
            "focusResults": false,
            "contextLines": 0,
            "filesToExclude": "user.*,dendron.hist.*"
        }
    },
    // looka t tasks
    {
        "key": "ctrl+s c",
        "command": "search.action.openNewEditor",
        "args": {
            "query": "",
            "isCaseSensitive": false,
            "matchWholeWord": false,
            "isRegexp": true,
            "triggerSearch": false,
            "focusResults": false,
            "contextLines": 0,
            "filesToInclude": "user.kevin.weekly.2022.10.04.md,user.kevin.monthly.2022.09.md,user.kevin.check.md"
        }
    },
    // Look across my zsh history 
    {
        "key": "ctrl+s z",
        "command": "search.action.openNewEditor",
        "args": {
            "query": "",
            "triggerSearch": false,
            "focusResults": false,
            "isRegexp": true,
            "includes": "zsh.hist.*"
        }
    },
    // Look across my dendron history
    {
        "key": "ctrl+s h",
        "command": "search.action.openNewEditor",
        "args": {
            "query": "",
            "triggerSearch": false,
            "focusResults": false,
            "includes": "dendron.hist.*"
        }
    },
```

## Debug
- cmd+shift+d: toggle debug pane

## Other
- ctrl+cmd+d: lookup now for mac

## Navigation
- terminal, prev: cmd+shift+h
- terminal, next: cmd+shift+l