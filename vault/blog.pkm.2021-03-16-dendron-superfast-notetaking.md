---
id: 6ed40fc3-6418-4d12-8a05-186adfb29d5b
title: Dendron - Super Fast Open Source Note-Taking inside VSCode
desc: ''
updated: 1615909953338
created: 1615908967085
layout: single
date: '2021-03-16'
category: pkm
header:
  teaser: /assets/images/1.jpg
toc: true
toc_sticky: true
excerpt: Building a better tool for thought
---

![](https://kevinslin-images.s3.us-west-2.amazonaws.com/images/2021-03-16-08-52-27.png)

I started [Dendron](https://dendron.so/) because I was frustrated with the lack of good note-taking tools while working as a developer at AWS. 

I wanted something with the ease of notepad, the structure of evernote, and the speed of redis.

Iterating over these features for nearly a decade led me to Dendron and its been battle-tested with my own collection of 30K+ notes. 

## VSCode, Lookup, and Hierarchies

Dendron is built into VSCode and also runs in VSCode compatible editors like VSCodium and Code Server. All notes are in plaintext markdown with frontmatter to store optional metadata at the top of the file. 

Creating and finding files works in seconds and work through the same interface. If a note doesn't exist, Dendron creates it. This interface is called [lookup](https://wiki.dendron.so/notes/a7c3a810-28c8-4b47-96a6-8156b1524af3.html) and it is used to quickly navigate note hierarchies. 

Dendron helps with organizing notes using [note hierarchies](https://wiki.dendron.so/notes/f3a41725-c5e5-4851-a6ed-5f541054d409.html) that are represented by dot delimited filenames. For example, `foo.bar.baz` would be represented in dendron as

```
.
└── foo
    └── bar
        └── baz
```

## Schemas, Markdown, and All the Links

Dendron gives you [schemas](https://wiki.dendron.so/notes/c5e5adde-5459-409b-b34d-a0d75cbb1052.html), optional YAML that you can associate with each hierarchy which describes their shape and allows Dendron to autocomplete and enforce your organization.

Dendron has its own flavour of markdown that includes additional functionality that enables, latex, mermaid diagrams, and the ability to include [content from other notes](https://wiki.dendron.so/notes/f1af56bb-db27-47ae-8406-61a98de6c78c.html). 

It also has [numerous ways](https://wiki.dendron.so/notes/3472226a-ff3c-432d-bf5d-10926f39f6c2.html) of linking your notes together. There are wikilinks to quickly link to any other note, backlinks to see what notes a given note is linked to, hierarchical links to find the parent and children of any note and relative links to go to a specific part of any document. 

## Publishing, Vaults, and Pods

Dendron comes with built-in [publishing](https://wiki.dendron.so/notes/579e379b-3eca-4676-b51c-c66eb26a11b8.html) - you can compile your notes to static HTML and publish them anywhere. [Dendron published sites](https://wiki.dendron.so/notes/3a82c5ff-7945-46ae-8bf9-3b2275fc6642.html) have been deployed on GitHub pages, S3, Azure, Vercel, Netlify, and more.  You have fine-grained control of what gets published and Dendron takes care of the details - any links to private notes from public notes are automatically overwritten with a custom 403 page during the built stage.

Dendron employs a vault-based model for storing notes where a [vault](https://wiki.dendron.so/notes/c6fd6bc4-7f75-4cbb-8f34-f7b99bfe2d50.html#vaults) is a logical collection of notes, typically backed by git. You can mix and match vaults in a [workspace](https://wiki.dendron.so/notes/c6fd6bc4-7f75-4cbb-8f34-f7b99bfe2d50.html#workspace) and store them in different places (eg. personal notes on dropbox but company notes locally on the computer). Dendron gives you one unified interface to reference all your notes across all vaults. These vaults can be imported natively in Dendron, either by a relative path or a remote git URL. 

Lastly, Dendron has a [pod system](https://wiki.dendron.so/notes/66727a39-d0a7-449b-a10d-f6c438185d7f.html) to help you import/export your notes from Dendron to any other tool and format. We have a JSON pod to export notes and metadata to JSON so you can [index it to elasticsearch](https://wiki.dendron.so/notes/401c5889-20ae-4b3a-8468-269def4b4865.html#analyze-notes-using-elasticsearch) or create custom visualizations. We have a markdown pod to convert Dendron markdown to regular markdown so that Dendron is interoperable with any tool that accepts markdown. 

## Today

To date, Dendron has been downloaded over 20 thousand times and powers everything from personal journals to team-based knowledge bases. We ship features weekly and has an active Discord community around note-taking that exchange hundreds of messages a day. My goal is to grow Dendron to be the best note-taking tool for developers, and in time, everyone. 

> "We are overwhelmed with information and we don't have the tools to properly index and filter through it. [The development of these tools, which] will give society access to and command over the inherited knowledge of the ages [should] be the first objective of our scientist" - Vannevar Bush, 1945


The north star of this project is to achieve Vannevar Bush's original vision - to build a tool of thought that can give humanity "access to and command over the inherited knowledge of the ages".

If you are reading this, you too, are now part of the journey. Let's take back control of our information and use it to build something better, together!