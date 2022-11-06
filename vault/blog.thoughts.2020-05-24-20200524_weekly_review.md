---
id: c4ee35be-2296-4566-a7c9-8328d9802a8a
title: '2020-05-24: Weekly Review '
desc: ''
updated: 1608779051573
created: 1596518032934
layout: single
date: '2020-05-24'
categories: thoughts
category: thoughts
header:
  teaser: /assets/images/conv-ilana-20200524.png
toc: true
toc_sticky: true
excerpt: 'Cognitive Technology, Economics of Open Source, and the State of Podcasting'
classes: wide
---

!["Subway Map as cognitive technology"](https://kevinslin-images.s3.us-west-2.amazonaws.com/images/conv-ilana-20200524.png)

# Preamble

Economics has been on my mind this week. Partially due to COVID, partially because I'm drawing closer to releasing [my note-taking app](https://dendron.so/) and I'm still trying to figure out the right business model for it. And whether it should be open source. 

# Structure

- Thoughts: What I'm thinking about based on articles I'm reading and things I'm working on
- Systems: Tools and workflows I'm experimenting with to more effectively learn and get things done
- Outputs: Things I'm working on in various stages of completion

# Thoughts

- Cognitive Technology
  - Articles
    - [Thought as a Technology by cognitivemedium.com](http://cognitivemedium.com/tat/index.html)
  - Quotes
    > Language is an example of cognitive technology: an external artefact, designed by humans, which can be internalized, and used as a substrate for cognition.
  - Notes
    - We think of technology as external gadgets that we can feel and touch - eg. iPhones, cars and planes. But if we think of tech as "the application of knowledge for practical purposes", then there's no reason why new ways of thinking wouldn't count. These "cognitive technologies" are especially powerful because they allow us to make sense of the world with completely new capabilities. Language allows us to control time through past and future constructs. Musical notation let us compose and share sounds. A subway map allows us to collapse geography from a continuous plane to discrete stops.
- The economics of open source businesses
  - Articles
    - [In 2019, multiple open-source companies changed course—is it the right move?
](https://arstechnica.com/information-technology/2019/10/is-the-software-world-taking-too-much-from-the-open-source-community/)
  - Quotes
    > Open source does not limit what you can do with the software ever. Full stop. 
  - Notes
    - Running an open source business was never simple and most made their money from providing hosted versions of their service. The entire value proposition of the cloud is to help customers host services that they care about. Successful open-source projects like Redis are by definition, hosted services that customers care about which is why AWS and other cloud vendors offer paid versions of these services. This is completely within their rights given the licenses and this has caused many former open-source companies to adopt restrictive non-open source business licenses on their products. This is a catch-22 - the popularity of many of these services came from their open source license but it's the same license that lets companies like AWS host the services without paying into the project. The two approaches companies are taking: making proprietary modules under non-open source licenses (eg. Redis) or going completely open-source (eg. Chef)
- The state of the open podcast ecosystem
  - Articles
    - [The open podcast ecosystem is dying — here’s how to save it](https://divinations.substack.com/p/the-open-podcast-ecosystem-is-dying)
  - Quotes
    > As with everything else in business, this isn’t really about money. It’s about power. And in the case of Spotify vs the Open Podcast Ecosystem, we’re witnessing a struggle that’s not between two opposing companies, but instead is between two opposing value chains with two different architectures.
  - Notes
    - I'm a big fan of podcasts and RSS feeds. They are open, distributed and grassroots. Anyone can publish a podcast that anyone else can subscribe to and there is no central entity that controls all of it. Unfortunately, this causes a bunch of ergonomic challenges for users (eg. no playlists, hard to search, etc), for advertisers (eg. no concrete analytics, lack of targeting) and for podcast hosts (eg. no central registry to publish to). Spotify is currently trying to consolidate the podcast market and has signed exclusive contracts with some of the most influential podcasters today. Like Amazon.com back in the day when it was a bookstore, having a centralized player helps both consumers and producers at the outset but does leave you to the whims of the central entity if it succeeds in capturing the market. 


# Systems

- Checklist for evaluating JS Based App Frameworks
  - Notes
    - Evaluating different frameworks this week and realized that while some projects like [reedwood.js](https://redwoodjs.com/) are compelling, certain dealbreakers make it a no go. My criteria for any framework is that it must support the following dependencies

```
- typescript (need)
- react (need) 
- react-router (nice to have)
```

- Checklist for debugging
  - Notes
    - Really into making checklists this week. Made a list of common strategies that I use when I get stuck

```
- sanity check
    - make sure simple things work
- walk away
    - nap
    - do something else
- check assumptions
    - requirements
    - solutions
- read source material
    - eg. RFC, API docs, source code
- KISS
    - is there a simpler solution? 
- what are alternative solutions?
    - come up with 3 alternatives
- go over past lessons
    - look at https://airtable.com/tbllUnXtBp3Erys72/viw0bR3zPkZgmAVZ8/recfkD5qo9OPbOM6k
- off by one error
    - typoes, eg: http instead of https
```
# Outputs

- [Dendron](http://dendron.so/)
    - Research SSR (decided this was premature optimization)
    - Integrate cognito auth with amplify UI
    - Integrate with dropbox based backend (too slow)
    - Start integration with electron
- [Thence Consulting](https://thence.io/)
    - Handle Operational Issues
    - Create patch to [discourse's getsatisfaction](https://meta.discourse.org/t/migrating-from-getsatisfaction-to-discourse/56197) script to import replies to discussions
    - Handoff Documentation
- Other
    - Started the [Seattle Building a Second Brain (BASB) Meetup
](https://www.meetup.com/Seattle-BASB-Meetup/)
