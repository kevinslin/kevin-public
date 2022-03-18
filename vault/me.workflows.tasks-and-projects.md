---
id: wg6ltqmwh7c6ccd58r8h6wq
title: Tasks and Projects
desc: ''
updated: 1647630160815
created: 1647630160815
---

## Summary
#stage.seed

If you prefer videos, you can see a video with detailed notes and annotations about my task workflow in the [Dendron Greenhouse Talk](https://wiki.dendron.so/notes/ordz7r99w1v099v14hrwgnp.html)

For more textual details, read on.

### Managing Tasks

My workflow for tasks is mostly bullet journal style -> https://wiki.dendron.so/notes/e65dfe53-41f7-4b16-b870-dadec1775497.html

At the beginning of the day, I create tasks in my daily journal. This applies my daily journal template - the annotated version is [here](https://gist.github.com/kevinslin/e849cbf3a13603fed8e4de5830b72209)

I keep templates for tasks due `weekly` and `monthly` as well. 

You can see the template that the Dendron team uses here -> https://handbook.dendron.so/notes/tBl65U3RF6E587tV7mie5.html
We use the following configuration for task notes. This means that tasks are created as `task.{year}.{month}.{day}.{name}`

```yml
    task:
        name: task
        dateFormat: y.MM.dd
        addBehavior: asOwnDomain
        statusSymbols:
            '': ' '
            wip: w
            done: x
            assigned: a
            moved: m
            blocked: b
            delegated: l
            dropped: d
            pending: 'y'
        prioritySymbols:
            H: high
            M: medium
            L: low
        todoIntegration: false
        createTaskSelectionType: selection2link
```

### Managing Projects

For projects, I use a `proj.*` hierarchy. Projects can have tasks associated with them. As well as tags.
