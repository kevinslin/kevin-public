---
id: qxnOYZVf33H5cUD2zXeP2
title: Twitter
desc: ''
updated: 1645330827350
created: 1645329750745
---

## Source

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">Thought this was a great write-up of the missing link with <a href="https://twitter.com/RoamResearch?ref_src=twsrc%5Etfw">@RoamResearch</a>  and tools like it. They make it incredibly easy to add notes  but after a while, make it incredibly difficult to find those notes again when needed <a href="https://t.co/nXefnpVe9g">https://t.co/nXefnpVe9g</a></p>&mdash; Kevin S Lin (@kevins8) <a href="https://twitter.com/kevins8/status/1493020520815546368?ref_src=twsrc%5Etfw">February 14, 2022</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>


## Discussion

Thought this was a great write-up of the missing link with @RoamResearch  and tools like it. They make it incredibly easy to add notes  but after a while, make it incredibly difficult to find those notes again when needed

***

tldr: roam's defining feature, bi-directional links, sound great in theory but break down in practice. if you rely on them as your primary means of finding notes, it quickly breaks down because there's no canonical starting point (by design). 

***

Disclaimer: I'm the founder of @dendronhq, a YC backed PKM tool focused on hierarchies and structured note-taking.  I'm not impartial but I've also spent the last decade thinking about this topic and can share some of my insights from that process.

When Dendron first launched as a PKM focused on structured notes back in 2021, it was during "peak-roam"
Everyone was talking about bi-directional links and a lot of people questioned even the viability of focusing on hierarchy  "post-roam"

***
Our focus was to both help add and retrieve notes, even when you have thousands of them.  The approach I settled on was "the hierarchy first approach to note-taking". This is what I use in my PKM that is now approaching 40K notes

https://blog.dendron.so/notes/3dd58f62-fee5-4f93-b9f1-b0f0f59a9b64/

***

This approach is at the heart of Dendron.  We bet that it wasn't hierarchies that were the issue but rather their implementation  Rigid, hard to use, and impossible to change - existing tools required that you defined the structure upfront
***

This did not map on to real-life where information is constantly changing.  This is why Dendron is about "flexible hierarchies" and "gradual structure".  

https://wiki.dendron.so/notes/683740e3-70ce-4a47-a1f4-1f140e80b558.html#how-is-dendron-different-from-x-note-taking-tool

***

At the end of his article, @danshipper compares PKM to programming

> information can be used in an infinite number of ways. So having a... system—like a programming language—is the best foundation

I think the first claim is spot on - information is too vast and too flexible
***

In order to manage information at scale, we need to give people the same power that programming languages and IDEs give developers. This tooling helps humans manage codebases that are millions of lines long - we can adopt the same concepts to PKM

https://wiki.dendron.so/notes/N9VxT7G5SovmncezBAGO2.html

***

I will push back on @danshipper's second claim 

> having an infinitely flexible organization system...is the best foundation for a note-taking system.

***

**Infinitely flexible organization system** sounds nice in principle (just like the idea of bi-directional links to avoid having to structure your notes) but the problem is us (humans). our memory is not infinite

***

our problem is not **how to represent information in its infinite facets** but rather, **how to filter an overwhelming amount of information down to a useful subset that we can hold in our head and make use of**
***

This is why, programming languages in 2020, are notable for the things that they prevent you from doing (eg. go has no inheritance, rust forces ownership checks, type checking is now increasingly the norm for former dynamic languages like python and javascript)

***

Just like programming languages have come around to the idea of adding constraints to better manage large programs, I think PKM tools will do the same with information.

The question is not what features we can add but what set of constraints we can enforce?

***
At Dendron, our defining constraint is the hierarchy. We think the way forward is with consistent structures that are flexibly defined.  What are your thoughts?


***

The reason I got into the PKM space is
1) I had a personal problem (too much information and couldn't make use of information I already encoutnered)
2) The tooling for how **humans** manage large amounts of information is an unsolved problem

At Dendron, the way were are approaching the problem is with consistent structures that are flexibly defined. This is the foundation on which other features like tags, bi-directional links, and graphs are built on top of. 

All this being said, its still early days and people's opinions will differ. These are my opinions. What are yours?

