---
id: bwdv0wywdaim1ksapvkfyad
title: 'Local-First Software: You Own Your Data, in Spite of the Cloud'
desc: 'Introduces the concept of "local-first software", applications that are local first but still have the same capabilties as cloud-based apps (chiefly real time collaboration)'
updated: 1655698043219
created: 1655689590682
tags: []
traitIds:
  - til
url: 'https://www.inkandswitch.com/local-first/'
date: 2022-06-19
published: false
---

## Summary
- source: {{fm.url}}

{{fm.desc}}

## Notes

Back in the day, software ran locally on one's computer. 
This worked great until you needed to collaborate or synchronize with another machine (or spill coffee on your current machine). 
And to solve this problem, we started putting software on ~~someone else's computer~~ the cloud. 
Software on the cloud solved one set of problems but introduced new ones - mainly lack of ownership, autonomy, and control of one's own data. 
And so we've come full circle with growing momentum around  **local-first** software. 

This article tries to define **local-first** software as software where data is primarly stored on one's own machine but also has sync and collaborative features as well. 
It establishes seven criteria for evaluating "local-first software":

1. Fast 
2. Multi-device 
3. Offline 
4. Collaboration 
5. Longevity 
6. Privacy 
7. User control Files

The second half of the article goes into length about [CRDTs](https://en.wikipedia.org/wiki/Conflict-free_replicated_data_type) and how they help solve the collaboration problem for **local-first** software with a git meet google doc approach to synchronizing state. The article talks about the success of synchronizing state in several sample applications using their own CRDT implementation and also goes over challenges with the approach (eg. performance problems when changes pile up and the inability to merge edits on the same property).  It concludes by positioning CRDTs as the way forward for collaboration with local-first software.

While this could be true, I wonder if an 80/20 solution is to use cloud based software as a bridge for collaboration while using local-first software to master the data. Collaborating on a document by definition implies some sort of, well, collaboration. What if we can use the strength of each paradigm for its designated purpose instead of trying to re-invent what the other already does so well? 

A very limited but real world example at Dendron - we master everything inside our company vault - a git repo that contains all our internal docs, sprint plannings, etc. When we do sprint planning, we will export the planning doc to google doc a few days ahead of time so that everyone can post their updates and leave comments. A few days after sprint planning, we then synchronize this doc with the note we have locally. We also do this for design reviews, all meeting notes, and tasks (via syncing with airtable). 

While I do think local-first software can do better at synchronizing state, if I want real time collaboration, I want google doc. If I want to have batched collaboration, I'd use git. And if I'm working by myself, I'd use my notes without sharing them outside of my network. The take away here is that there is room for all these solutions and that they all bring something to the table. We don't want to re-invent the world with a single methodology but rather, integrate the strength of different paradigms, taking the best aspects of each, and creating something that is better than the sum of its parts. 

***
<!-- 
Dendron is **local-first** software. But we're not idiomatic about it. We think it makes sense for your knowledge to be mastered locally because you should have **ownership, autonomy, and control** over your own data. But if you want real time collaboration, we think its better that we support seamless integration with existing solutions like google docs vs try to replicate it locally. 

Ownership, autonomy and control don't mean much if you can't get a result. Pragmatism. 

Being able to make use of your information in the most effective way possible. 
We think this is by using gradual structure in the form of fleixble hierarchies, consistently defined. The flexibility extends to lett
Flexibility is key. 




The authors already acknolwedge that there is usually some collaboration 



Concludes with the opnion that CRDTs are the way to acchieve collaboration in local first software -->


<!-- While I think this can work, I also think it's limited in use - ultimately, you will have merge conflicts and these conflicts get worse the longer software 

CRDTs will probably be the best we can do in terms of consistency for **local-first** software.  -->


<!-- If you squint, the dichotomy between **cloud vs local-first** software is actually captured 
[CAP theorem](https://en.wikipedia.org/wiki/CAP_theorem) - consistency, availability, partition tolerant applies to local first software. 

Cloud software is consistent and partition tolerant. It is not always available. 

Local software is available and partition tolerant (there are no partitions, you have all the data). It is not consistent as your machine is meant to work without being connected to the network.  -->

This article is trying to make local software consistent. 

## Thoughts
- interesting distinciton that collaboration/sync is the chief difference between local and cloud based software
- feels like a there and back again journey - we migrated to the cloud to not worry about local storage but we are now looking to come back because we realized we gave up something important in the process
- git is a great example of trying to capture the best of both worlds but is too difficult to use for popular adoption
- define `ownership` as perceived "agency, autonomy, and control"
  - technically, everything can be downloaded/scrapped/obtained but there's a world of difference for software that is designed to be this way 

## Lookup
- [[Local-First Software: You Own Your Data, in Spite of the Cloud|dendron://highlights/articles.local-first-software-you-own-your-data-in-spite-of-the-cloud#local-first-software-you-own-your-data-in-spite-of-the-cloud]]
