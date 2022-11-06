---
id: 271768e5-5acb-4aec-9275-d692fb6f0a8b
title: 2020-06-07 Weekly Review
desc: ''
updated: 1608779106214
created: 1596518032936
layout: single
date: '2020-06-07'
categories: thoughts
category: thoughts
header:
  teaser: assets/images/conv-ilana-20200607.png
toc: false
toc_sticky: false
excerpt: 'George Floyd, Antifragile Democracy, and Chekhov''s Gun'
classes: wide
---

![](https://kevinslin-images.s3.us-west-2.amazonaws.com/images/conv-ilana-20200607.png)

 Black lives matter. This has been an incredibly maddening, heartbreaking, and sad week in American history. It feels like we are living in a twisted groundhogs day where the same injustices keep happening. While there's nothing I can say that hasn't been said already, I want to take a public stand on the issue and share my thoughts on how this came to be. 


# WHAT I'M THINKING ABOUT

- George Floyd and Shining Light on Injustice
  - Articles
    - [Dust in the Light – Stratechery by Ben Thompson](https://stratechery.com/2020/dust-in-the-light/)
  - Quotes
    > The Internet is an amoral force — it can affect both positive and negative outcomes — but what cannot be underestimated is how gargantuan a force it is.
  - Notes
    - Two weeks ago, it was hard to imagine anything that would eclipse COVID. And then George Floyd happened. A single [video](https://www.nytimes.com/video/us/100000007159353/george-floyd-arrest-death-video.html) has sparked worldwide outrage and a demand for justice. While the civil rights act passed in 1964, the racial divide has not. Say what you will of social media but without it, its unlikely that George Floyd's story would have seen the light of day. For better and for worse, facebook has become the [fifth estate](https://venturebeat.com/2019/10/17/mark-zuckerberg-on-why-the-world-needs-facebook-and-the-fifth-estate/) and is shining a light on all parts of humanity. The challenge of our generation is to figure out how to respond. 
- Is Democracy Antifragile?
  - Articles
    - [Nassim Taleb: A Definition of Antifragile and Its Implications by fs.blog](https://fs.blog/2014/04/antifragile-a-definition/)
  - Quotes
    > Crucially, if antifragility is the property of all those natural (and complex) systems that have survived, depriving these systems of volatility, randomness, and stressors will harm them.
  - Notes
    - Nassim Taleb defines antifragile as the opposite of fragile - a trait that makes something stronger the more stress you put on it. This past week, I've been thinking about democracy and whether it is antifragile. In theory, it should be - as wrongs and inequalities are revealed, democracies should be able to put in safeguards and countermeasures to course correct. But we live today in a democracy that seems unable and/or unwilling to face the greatest challenges of our day (eg. pandemics, climate change, healthcare, etc). Are we antifragile?
- Everything Happens for a Reason
  - Articles
    - [Chekhov's Gun - Wikipedia](https://en.wikipedia.org/wiki/Chekhov%27s_gun)
  - Quotes
    > Chekhov's gun ... is a dramatic principle that states that every element in a story must be necessary, and irrelevant elements should be removed.
  - Notes
    - I grew up reading fantasy. Stories play a special place in my life. In a story, everything has a purpose and everything happens for a reason. I would like to think that life works the same way. I believe that predestination is a thing - not in the supernatural sense but in the sense that we can choose, with our actions, to give a purpose to the things that have happened.

# SYSTEMS

- Use [Puppeteer](https://github.com/puppeteer/puppeteer) to scrape [aTimeLogger](http://www.atimelogger.com/)
  - Notes
    - I track the time I spent on areas I care about using aTimeLogger. I also keep a daily journal of things that happened during the day. Wrote a script to merge the two into a single document to give me better context for how I spend my time
- Using [ansible](https://docs.ansible.com/) to publish blogs
  - Notes
    - Since I've been posting on my blog a lot more, I decided to automate the setup of these weekly reviews. Created an ansible playbook that will make this weekly review template and populate it with the right links and metadata.
