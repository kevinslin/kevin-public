---
id: 34f59583-4e37-4247-be87-f95abfc1d7b0
title: '2020-05-17: Weekly Review'
desc: ''
updated: 1608779022622
created: 1596518032934
layout: single
date: '2020-05-17'
categories: thoughts
category: thoughts
header:
  teaser: /assets/images/conv-second_system.png
toc: true
toc_sticky: true
excerpt: 'Second-system effect, Tools of Thought, Twitter Lists'
classes: wide
---

!["Second System"](https://kevinslin-images.s3.us-west-2.amazonaws.com/images/conv-ss1.png)

# Preamble

This week was mostly about shipping the new [Eterna platform](http://eternagame.org/).

> Eterna empowers citizen scientists to invent medicine through molecular design. You play by solving puzzles using RNAs, tiny molecules at the heart of every cell.

Been working with the Stanford lab as their AWS specialist to help them ship their new research platform which has been re-written for speed, simplicity and scale. Play games and fight COVID. Now that's a compelling value proposition.

# Structure

- Thoughts: What I'm thinking about based on articles I'm reading and things I'm working on
- Systems: Tools and workflows I'm experimenting with to more effectively learn and get things done
- Outputs: Things I'm working on in various stages of completion

# Thoughts

- Second System Effect (aka The Second Law of Thermodynamics)
  - Articles
    - [Second-system effect](https://v01.io/2020/05/04/second-system-effect/)
  - Quotes
    > A simple, well-functioning system reaches its limits and is then replaced by a much too big, bloated, overengineered system due to exaggerated expectations and exaggerated self-confidence – and everyone is unhappy.
  - Notes
    - The second system effect could be used to describe most large software systems I've ever worked with. While this probably happens across verticals, I think it's particularly egregious in software because it's so *simple* to create new code (as opposed to building a bridge). I used to run a podcast at Amazon where I interviewed our principal engineers and to the individual, they all espoused **simplicity** as a guiding principle. This is something I'm trying to keep in mind as I'm in the process of re-writing [Dendron...](http://dendron.so/)
    - My wife drew a comic to illustrate the second system effect for when I build our dog a new house

!["Second System"](https://kevinslin-images.s3.us-west-2.amazonaws.com/images/conv-second_system.png)

  - Tools For Thought
    - Articles
        - [As We May Think by Vannevar Bush](https://www.theatlantic.com/magazine/archive/1945/07/as-we-may-think/303881/)
        - [Why books don't work by andymatuschak.org](https://andymatuschak.org/books)
        - [Roam – Tool for Thinking ](https://news.ycombinator.com/item?id=21440289)
    - Quotes
    > Instruments are at hand which, if properly developed, will give man access to and command over the inherited knowledge of the ages. The perfection of these pacific instruments should be the first objective of our scientists as they emerge from their war work - Vannevar Bush
    - Notes
        - Our minds are not equipped to deal with the flood of information we need to keep up with today. While our physical prowess has increased manyfold, our tools of thought have remained much the same as they were 50 years ago when Vannevar Bush first published *As We May Think* 

# Systems

- Using [TweetDeck](https://tweetdeck.twitter.com/) to browse twitter via lists
    - Notes
        - I've found twitter to be too noisy and wanted to utilize their list feature. TweetDeck lays out lists in vertical columns along the page. 
- Using [jq](https://stedolan.github.io/jq/) with [json2csv](https://github.com/jehiah/json2csv) for projects involving json and csv
    - Notes
        - In order to browse twitter by lists, I needed to create some lists. Used the opportunity to also *Mary Kondo* my twitter feed. And because I'm a developer, I used `jq` and `json2csv` within the following pipeline:  `twitter api -> jq -> json2csv -> google sheets` 

# Output

- [Dendron](http://dendron.so/)
    - 2 customer interviews
    - read first 1/3 of [The Mom Test](https://amzn.to/2LyDexq)
    - feature work
- [Thence Consulting](https://thence.io/)
    - Flipped the switch to launch new version of [Eterna platform](http://eternagame.org/) 
        - Been working with the Stanford lab on the backend infra for the last month in anticipation for the release
