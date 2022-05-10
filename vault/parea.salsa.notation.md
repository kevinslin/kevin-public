---
id: drsxph834ccxcknuecpkmja
title: Notation
desc: ''
updated: 1652144571632
created: 1652143331077
---

## Prototype

```
[{num = 1}x] [{modifier}] {target}.{action} [(arguments)]
```

## Syntax

### Modifiers
- syntax:
    - l: left
    - r: right
    - b: both hands

### Target
- syntax:
    - e: elbow
    - h: hand
    - s: self
    - f: follow

What is doing the action

### Actions

### cross over
- alias: xo
- default: `pre: xbody`

Changing directions

### position
- alias: pos

A position to get to

### offer

Making an offer

### hold

Regular open hold position

### turn

### rest
    - step
    - throw
    - catch
    - bump

### Arguments
- bo[dy]: 
    - behindBack
- t[arget]
- [pos]ition:
- [pre]sets: 
    - hl: hammer lock
- dir[ection]
    - default: c
    - values: 
        - cc: counterclockwise
        - c: clockwise
- rot[ation]:
    - values:   
        - {num}x: number of times