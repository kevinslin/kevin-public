---
id: e1455752-b052-4212-ac6e-cc054659f2bb
title: It's Not You - It's Your Knowledge Base
desc: ''
updated: 1608778910628
created: 1596518855656
tags:
  - manifesto
layout: single
date: '2020-03-30'
category: pkm
header:
  teaser: /assets/images/1.jpg
toc: true
toc_sticky: true
excerpt: Thoughts on organizing and finding information in a digital age
---

!["Human with information overload"](https://kevinslin-images.s3.us-west-2.amazonaws.com/images/1.jpg)

## Information Overload

Have you ever struggled to find information inside your knowledge base or note-taking app? When you did find what you needed, was it after a long time searching and wading through lots of results?

If so, this is likely because of information overload - when every human being has access to the sum of all human knowledge, finding any specific bit of it becomes a daunting task.

![Human running away from too much information](https://kevinslin-images.s3.us-west-2.amazonaws.com/images/2.jpg)

For the modern knowledge worker, the amount of information we need to track far exceeds what can actually fit in our heads. As a consequence, we externalize that information using tools like Evernote, Notion, and Confluence.

## Existing Tools

![Knowledge worker using Evernote, notion, and confluence to organize information](https://kevinslin-images.s3.us-west-2.amazonaws.com/images/3b.jpg)

The problem with these tools is that the more information we put in, the harder it becomes to get it back out. This is because existing tools lack the capability of organizing large amounts of information and why most of us rely on search to find what we need.

![Knowledge worker organizing information using notion, Evernote, and confluence](https://kevinslin-images.s3.us-west-2.amazonaws.com/images/4.jpg)

## Search

Search is great when you are looking through **the internet**, **know what you’re looking for** and need **a good result**. For example, a google search for “the height of Mt Everest” will give you exactly what you need.

Search fails when you are looking through your **knowledge base**, might **not know the exact query**, and are looking for a **highly specific result.** For example, say you saved an article about government tax breaks. Searching those exact terms together might yield no results but searching for “government” or “tax” individually might flood your screen with non-relevant data.

## Keywords

The fundamental problem with a search centric model for managing information is that it is based on keywords.

Here, a keyword is defined as any term that has ever been or might ever be used in the human language. To get the right result, two things need to happen.

1. Out of the vast universe of all possible keywords, you need to pick out the right keywords that **match the relevant document you are looking for** when you are **looking for it.**
2. Out of the vast universe of all possible keywords, you need to have picked out the right keywords that **you thought might match a future query** when you **first created the document.**

The odds of both things happening, especially over long periods, are astronomical. It is like trying to find a star in the night sky by just its description.

![Knowledge worker finding information using keywords](https://kevinslin-images.s3.us-west-2.amazonaws.com/images/4b.jpg)

The reason keywords don’t work is because they require people to do something that the human brain is not good at - working with and remembering an unbounded number of things.

## Organizing

The alternative to keeping track of everything individually is organizing.

Here, organizing is defined as a means coming up with systems to simplify your data so that you can more easily access it later.

The most common way that people organize information is to group related concepts into topics. By grouping related information, you reduce the cognitive load required to keep track of individual items.

The psychological term for this is called [chunking](<https://en.wikipedia.org/wiki/Chunking_(psychology)>) and it is something that the brain is naturally good at. Even unconsciously, we will create associations between things. This process is so powerful that we now have [unconcious bias training](https://en.wikipedia.org/wiki/Unconscious_bias_training) to **train ourselves not to do this** when it results in discriminatory behavior.

![Knowledge worker chunking information into topics](https://kevinslin-images.s3.us-west-2.amazonaws.com/images/5b.jpg)

## Chunking

The problem with chunking is that it cannot keep up with the sheer amount of information we deal with today.

If you try to group all the information into chunks, one of two things will happen.

1. You have too many chunks and keeping track of chunks becomes overwhelming
2. You have a few chunks with too many things in them which causes you to lose the benefits of chunking in the first place

![Knowledge worker trying to find information but being overwhelmed with chunks](https://kevinslin-images.s3.us-west-2.amazonaws.com/images/6.jpg)

## Indirection

The solution to too many chunks is indirection.

> "All problems in computer science can be solved by another level of indirection"
> -- David Wheeler, Computer scientist

When the complexity of information becomes too much at one level, we simplify it with a layer of indirection that pushes the bulk of the complexity to a new level.

For information, this means creating hierarchies to group related chunks. We can keep doing this until the number of chunks at any given level of the hierarchy becomes manageable. At this point, our data starts to take the shape of a tree.

![Knowledge worker modeling information as a tree](https://kevinslin-images.s3.us-west-2.amazonaws.com/images/7.jpg)

## Trees

Trees are one of the most important data structures in computer science and are used extensively in every database ever designed. These are systems that actually store all the world's information.

Finding information in a tree can be done in [logarithmic time](https://en.wikipedia.org/wiki/B-tree#Time_to_search_a_sorted_file) - in layman's terms, it means you can quickly search through large amounts of data using very small trees. An example of this in real life is the [dewey decimal system](https://en.wikipedia.org/wiki/Dewey_Decimal_Classification). This system is used by libraries to assign unique identification labels to every book ever published.

## Human Compatibility

Time for a quick recap - the human brain is not good at remembering an endless amount of things. This is why keywords fail. We, therefore, chunk related concepts into topics. This too runs into problems when we end up with too many chunks. The solution to this is to group related chunks using trees.

![Recap of knowledge worker putting together a tree - Part I](https://kevinslin-images.s3.us-west-2.amazonaws.com/images/7a.jpg)
![Recap of knowledge worker putting together a tree - Part II](https://kevinslin-images.s3.us-west-2.amazonaws.com/images/7b.jpg)

When the number of chunks at any given level exceeds what is easy for us to remember, we can apply _another level of indirection_ by grouping related chunks and pushing it further down the tree.

![Knowledge tree with a layer of indirection applied](https://kevinslin-images.s3.us-west-2.amazonaws.com/images/9.jpg)

This whole process is recursive - you can apply it at any level of the tree as many as times as you need to make the information comprehensible.

![Knowledge tree with multiple layers](https://kevinslin-images.s3.us-west-2.amazonaws.com/images/10.jpg)

This ensures that the number of chunks you need to track at any given level stays comprehensible no matter how much information you have.

Thinking about information in terms of trees reflects how humans naturally think about things and there is increasing evidence that the [human brain is hard-wired to think in terms of hierarchies](https://www.sciencedaily.com/releases/2008/04/080423121430.htm)

## Challenges

Today’s knowledge bases support the concept of trees through the use of folders and tags. But this is not enough. Folders and tags haven't changed much in the last fifty years and run into the following challenges when used in tree form.

1. **Consistency**: Ability to apply the same tree everywhere
2. **Manageability**: Ability to update the tree everywhere
3. **Usability**: Ability to quickly find data using the tree

## Consistency

There's overhead involved in creating trees. Even simple domains can have dozens of branches. We have enough trouble simply tagging our data, asking us to re-create entire hierarchies for any number of things is simply too much work.

![Replicating knowledge tree](https://kevinslin-images.s3.us-west-2.amazonaws.com/images/11c.jpg)

## Manageability

A tree-based organization structure tends to be the reflection of our mental model of a particular domain. The thing about mental models is that they change. Either the underlying domain changes or our understanding of it changes. Either way, the structure of the tree changes. But when it does, making sure all our existing trees reflect that change is not easily done with current tools.

![Updating knowledge tree](https://kevinslin-images.s3.us-west-2.amazonaws.com/images/12.jpg)

## Usability

Even if we organize data as trees, existing tools have little to no support helping us find data using hierarchies. We end up having to manually click through our trees or performing keyword searches that ignore our hierarchies completely.

![Using knowledge tree](https://kevinslin-images.s3.us-west-2.amazonaws.com/images/13.jpg)

## Searching for an Answer

The reason we don't use trees to organize and find information today is that existing tools make it unfeasible to do so. The means of organizing information in a "modern" knowledge base is the same as it was fifty years ago - folders and tags.

Ever since Google became the dominant way to find information on the web, knowledge bases gave up trying to improve on organization. Why organize when you can search? The problem is that **search doesn't work in these tools** and continues to **get worse as the amount of information we track keeps growing**.

## Creating a Better Tool

What if we could create a tool that made it possible to organize and find the information we cared about, no matter how much information we had? What could we do with such a tool?

Thinking about such a tool is something I've done for much of the last decade. More than think about it, I've built such a tool and used it to manage a personal knowledge base of +20k notes. This tool has become my second brain and I use it for just about everything that I do.

A year ago, I left my job at Amazon to build a consumer-ready version of this tool. It is called [Dendron](https://dendron.so/) ~~and will be entering into early access in early May~~ and is now out in [preview](https://www.dendron.so).

Note that using Dendron will not magically make information overload go away. You still have to do the hard work of organizing your information. But Dendron finally gives you a sane way of doing so.

This post is not a sales pitch and I'd be lying to say we got it all figured out. Rather, it is an invitation to come along on a journey to find answers to what I consider to be one of the biggest challenges of our age. How do we make sense of the world we live in?

If this message resonate, lets connect. You can join the conversation on dendron's [discord server](https://discord.gg/AE3NRw9) or contact me directly at kevin@dendron.so. 

It is said that life is about the journey - what greater journey is there than the one to make sense of it all?

## Notes

- **Thanks** to [Luke Sernau](https://www.linkedin.com/in/luke-sernau-40394648/) and [Ilana Lin](https://www.linkedin.com/in/ilana-l-539930167/) for proofreading drafts of this
- **Special thanks** to [Ilana Lin](https://www.linkedin.com/in/ilana-l-539930167/) for creating the beautiful illustrations in this post
