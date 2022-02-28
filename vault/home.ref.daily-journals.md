---
id: f676aa8a-e73d-40c5-b790-29016399ce30
title: Daily Journals
desc: ''
updated: 1611179203183
created: 1611178493632
---


This goes over how I structure my daily journals in [Dendron](https://dendron.so/notes/b0fe6ef7-1553-4280-bc45-a71824c2ce36.html). 

I use the following [template](https://dendron.so/notes/c5e5adde-5459-409b-b34d-a0d75cbb1052.html#schema-templates) for my daily notes

```markdown
---
...
me:
work:
---

## Checklist
- [ ] journal
- [ ] email
- [ ] slack

## Thoughts

## Mantra

## Grateful

## Tasks

## Events

## Plus

## Minus

## Next
- --- morning
- --- noon
- --- evening
```

An explanation of fields:

## frontmatter

### work

I use this to track how I feel I've done work wise, from a 0-2 rating. 0 means I've done no work whatsoever, 2 means I've spent +5h doing productive work on Dendron

### me

I use this to track if I've taken care of myself (eg. take breaks, exercise, spent time with friends and family, etc)

0 means I've not spent any time on self care, +2 means I feel rejuvenated and recovered more than I worked today.


## sections

### thoughts

use this section for free form writing when I wake up in the morning

### mantra

usually have something that I like to focus on for a period of time.

### grateful

this is my gratefullness journal. top three things I'm grateful for. I try to be specific:  
>  I'm grateful for my health 

This is generic and I can just copy and paste this without thinking about it

> I'm grateful that I can walk without pain

This is more specific and refers to the fact that I had a bad tendon issue that prevented me from running for a year.

### tasks

I use this to track things I do for the day. Most of my tasks relate to [Dendron](https://dendron.so) which I track in a separate journal so this is for personal tasks. Use github flavored markdown format

### events

use this for a high level overview of how my day went

### plus

list out things that went well

### minus

list out things that could be better

### next

remind me of items that I should get to the next day, broken into sections

## additional notes

I used to be a lot more into time tracking back in the days when I was [[consulting|blog.aws.2020-01-06-everything_i_learned_from_aws_consulting]]. Since I've stopped to work full time on Dendron, it didn't make sense to record my time as closely and it was just causing overhead which is why I switched to the 0-2 system. 

Below is how my events section used to look when I did time tracking. I used [aTimeLoggr](http://www.atimelogger.com/) to track on my iOS device and had a script to scrape my daily times to my journal so I had a daily log of what I did at what time.

I grouped different areas by the following prefixes:
- w: work - work I don't like doing 
- p: projects - work I want to be doing
- pe: people - spending time with people
- h: hobby - doing stuff that I enjoyed

Depending on the day, I might add links or details to other things that happened in the day. Below is an actual entry from `2020.09.25`.

```markdown
## Events
- [8:20 am] w.admin:
- [8:23 am] p.dendron:
- [8:26 am] pe.ilana:
- [8:59 am] p.dendron:
- [12:00 pm] pe.social:
    - [[Mike|meet.journal.2020.09.25.mike]]
- [12:44 pm] pe.ilana:
- [1:06 pm] h.health:
    - nap
- [1:20 pm] p.dendron:
- [3:02 pm] h.health:
    - run
        - run 6 laps around track
- [3:53 pm] pe.networking:
    - [[Basb|meet.journal.2020.09.25.basb]]
    - [[Jeff|meet.journal.2020.09.25.jeff]]
- [5:42 pm] w.admin:
- [5:53 pm] pe.ilana:
    - dinner
- [6:21 pm] h.break:
    - play game
- [7:12 pm] h.break:
- [7:28 pm] p.dendron:
    - work
- [8:13 pm] pe.ilana:
```
