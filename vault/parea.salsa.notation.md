---
id: drsxph834ccxcknuecpkmja
title: Notation
desc: ''
updated: 1659407207670
created: 1652143331077
---

## Prototype

```
[{num = 1}x] [{modifier}] {start}.{action} [(arguments)]
```

## Examples

```
hright
```

## Syntax

### Modifiers
- syntax:
    - l: left
    - r: right
    - b: both hands


### Start
- values: [[BodyPart|dendron://kevin-public/parea.salsa.schema#bodypart]]
- syntax:
    - e: elbow
    - h: hand
    - s: self
    - f: follow

What is doing the action

### Actions

What is being done
- [[Loop|dendron://kevin-public/parea.salsa.notation.action.loop]]
- [[Pull|dendron://kevin-public/parea.salsa.notation.action.pull]]
- [[Raise|dendron://kevin-public/parea.salsa.notation.action.raise]]
- [[Hold|dendron://kevin-public/parea.salsa.notation.action.hold]]


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

#### [t]arget
- [[BodyPart|dendron://kevin-public/parea.salsa.schema#bodypart]]

what body part is being targeted

#### [pre]sets
- [[Xbody|dendron://kevin-public/parea.salsa.notation.preset.xbody]]
- hl: hammer lock

####
- bo[dy]: 
    - behindBack
- t[arget]
- [pos]ition:
- dir[ection]
    - default: c
    - values: 
        - cc: counterclockwise
        - c: clockwise
- rot[ation]:
    - values:   
        - {num}x: number of times