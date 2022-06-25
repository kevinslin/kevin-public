---
id: VRS0K4m58DP5qYSGqeYNI
title: Discord
desc: ''
updated: 1645332020301
created: 1645330354093
---

## Source
- https://discord.com/channels/717965437182410783/755431846254477422/944679369589284884

## Discussion

If I were to sum up the authors main points: markdown is not a good fit as a modern content language because it is too basic, has no common standard, and is not structued enough to support modern features (eg. blocks)

I agree that markdown makes it hard to do structured text (dendron parses markdown into an internal json representation using unified and uses it to support features like note references). The step forward (imo) isn't to get rid of markdown though but to augment it

I disagree on most of the other points. its true that markdown is basic and lacks a firm standard but when we're authoring markdown, its not for pixel perfect content but to capture down thoughts with basic formatting and without friction. as for lacking structure, the author mentions that markdown supports structure using yaml and frontmatter (part of what dendron does) but says this doesn't go far enough. i think it works for the 80/20 use case. at the end of the day, I don't see markdown going anywhere because it is simple and has wide adoption. this is why json, and not xml, is the modern transport format for the web. 



I’m also not worried about markdown going anywhere, and I think that human readability’s role in reducing authoring friction (author from the comfort of any tool) is undersold in that piece when thinking about md for notes vs for publishing corporate CMSs (the author’s company target). I want to keep thinking about how it was originally designed for a non-programmer community (this was very surprising to me), as it makes me think about how the needs of different audiences may steer how a language is used more forcefully than what the “original designers” hoped. 

I look at other markup languages for specific domains like diagramming or equations (like argdown, mermaid, Pinot, latex), and am very keen to see what happens once more non software dev type people adopt them! I wonder how many dendron users are discovered markdown / these special markups for the first time because of dendron. Markdown editors historically have introduced me to a wealth of DSLs, even for sheet music and guitar frets. https://hackmd.io/s/features#UML-Diagrams 

What’s great about the code block model is that other renderers can just ignore the fences that they don’t know what do to with, it’s not like it breaks the file completely.
