---
id: r5wsyzfvhzdx889g0rdpcyw
title: Path Handling in VSCode
desc: ''
updated: 1662829894454
created: 1662828803765
tags: [mw]
traitIds:
  - til
date: '2022-09-10'
---

Did some exploring of how vscode resolves filepaths depending on the [[dendron://dev/vpkg.vscode.ref.workspace]] configuration. 

Observations:
- file path resolution works as expected except when inside a [[dendron://dev/vpkg.vscode.concepts.multi-root-workspace]]
  - inside a multi root workspace, absolute paths are resolved via the root of the **workspace folder** you **current opened file** happens to be in
  - if your current opened file is outside of any listed **workspace folders**, the root is resolved as the first workspace folder 

This might sound a little confusing written out so some examples below.

### File Layout
```
- root/ 
  - home.md
  - note.md
  - a/
      - home.md
      - note.md
  - b/
      - home.md
      - note.md
  - c/
      - home.md
      - note.md
```

### Workspace Settings
```json
{
	"folders": [
		{
			"path": "c"
		},
		{
			"path": "b"
		},
		{
			"path": "a"
		}
	],
	"settings": {}
}
```

### Aliases
- AP: absolute path
- RP: relative path

### Resolving paths from outside a workspace folder
- testing from `root/home.md`
  - AP: `[](/note.md)`: root/c/note.md  <-- the `/` resolves to `root/c` because it is the first workspace folder from our worspace settings
  - RP: [](note.md): root/note.md

### Resolving paths from inside a workspace folder
- testing from `a/home.md`
  - AP: `[](/note.md)`: root/a/note.md
  - RP: `[](note.md)`: root/a/note.md

## Lookup
- [[vpkg.vscode]]
- [[vpkg.vscode.ref.workspace]]