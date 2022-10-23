---
id: psy3lsv0a4jz04f4l49qt8q
title: Development Proces
desc: ''
updated: 1666393127654
created: 1666219055079
---

My mental model for the modern software development process (MSDP)

> NOTE: this does not cover:
    - domain specific knowledge (eg. databases, security, scaling, etc)
    - specific technologies used in app development (eg. databases, queues, infra)

## Three Phases of MSDP
- there are three phases of MSDP
    - prepare: scope out tasks that need to be done
    - exceute: execute on the tasks that need to be done
    - release: deploy tasks done and maintain forever

## End to End MSDP
- each phase of MSDP can be further divided into more granular steps

- prepare
    - plan: scoping out tasks
    - issue: creating and assigning tasks 
- execute
    - code: writing code 
    - qa: testing
    - commit: version control
    - review: review process
    - docs: internal and external docs, api docs
- release
    - deploy: deploying code to staging/production
    - operate: running a service after its been deployed: dashboard, monitoring
    - loopback: learnings, communicate changes upstream to start the cycle again

> Source:  this is heavily inspired by the [gitlab master plan](https://about.gitlab.com/blog/2016/09/13/gitlab-master-plan/)

> NOTE: an alternative way of thinking about this is the following three roles
- product work
- qa work
- ops work

Increasingly, this is all done by a single person and encasulated in what I call the **modern software development workflow** (MSDW)

In the next section, we're going to go over each step in detail and go over some examples, challenges, and products in each space. Each section will have the following structure:

- requirements: what steps make up each particular step
- examples: concrete examples of each step in practice
- challenges: current challenges facing developers at each step
- solutions: what is available at each step

> NOTE: this is not meant to be comprehensive but rather an 80/20 of showing big usecases