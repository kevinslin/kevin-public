---
id: 7f197479-279e-4b1e-9edd-21bf2da423b0
title: Tools
desc: ''
updated: 1627439206836
created: 1616285026176
---

A collection of some tools I use

## Knowledge Management
- Dendron: almost everything
- evernote: web clipper
- google keep: mobile notes writer
- ia writer/git journal: private notes mobile reader
- published dendron notes via safari : public notes, mobile reader
- airtable: tracking finances
- alfred: app launcher

## Dendron Setup
- I keep 3 versions of vscode (vscode, vscode insider, vscodium)
- I have a mac so I usually have two windows of each version running at any time 
    - this is because `CMD+~` lets you toggle btw two different windows of the same program
- each vscode version also has a custom keyboard shortcut which I've configured using alfred
- vscode versions:
  - vscode
    - used for coding
  - vscode insider
    - two windows, one focused on dendron specific notes and one for personal notes
        - both windows use the same vault but different workspaces (this is because vscode does not let you have multiple windows open to the same workspace)
        - because you can define a custom path (<https://dendron.so/notes/401c5889-20ae-4b3a-8468-269def4b4865.html>) for your vault, you can still point two workspaces to the same vault
    - vscodium
        - two windows, one focused on <https://dendron.so> documentation (use as reference as well as to update) and another for an alternate workspace i'm working on (eg. <https://aws.dendron.so>)
- the idea here is that I can access any note in any workspace using at most, two keyboard shortcuts.

### Workspace and Vaults
- file structure
```
.
├── workspace
│   ├── kevin-personal
│   └── kevin-work
└── vaults
    ├── kevin-public
    ├── kevin-blog
    ├── kevin-private
    ├── people
    ├── org-private
    ├── org-admin
    └── ...

```
- sample dendron.yml from `kevin-workspace`
```
vaults:
  - fsPath: ../../vaults/kevin-public
  - fsPath: ../../vaults/kevin-private
  - fsPath: ...

```
- sample `dendron.code-workspace
```json
        {
            "path": "../../vaults/kevin-public"
        },
        {
            "path": "../../vaults/kevin-private"
        },
```


## Windows
- Programs
  - Firefox & Chrome: Browsers
  - Launchy: Program launcher


## CLI

- ncdu: visualize du command
