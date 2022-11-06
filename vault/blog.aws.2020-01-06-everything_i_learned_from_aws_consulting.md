---
id: 07112ed8-c156-4c5c-911d-532845350777
title: Everything I Learned from One Year of AWS Consulting
desc: ''
updated: 1651879589699
created: 1596416622319
tags:
  - area.business
date: '2020-01-06'
categories: aws
category: aws
header:
  teaser: /assets/images/20200106-splash.jpg
toc: true
toc_sticky: true
excerpt: Thoughts and learnings from 1 year as an AWS consultant
layout: single
---

!["Hindsignt of a year of AWS consulting"](https://kevinslin-images.s3.us-west-2.amazonaws.com/images/20200106-splash.jpg)

They say that hindsight is 2020 - which makes the start of this year the perfect time to reflect on the last.

2019 was a tumultuous year - both from a macro world socio-economic oh-my-god-what-is-happening standpoint and from a micro individual oh-my-god-what-am-I-doing standpoint.

On the individual front, I made three life-altering decisions in 2019:

1. I left AWS after five years to pursue my own startup(s)
2. I started three businesses
3. I got married

This post will focus on the first two decisions and how they played out over the course of my first year outside of big tech. As a first-time entrepreneur, I hope the learnings here will help others making similar transitions.

## Background

I [left Amazon](https://www.geekwire.com/2018/kevin-s-lin/) around the beginning of 2019 to pursue my own startup ambitions. While I loved working at Amazon, I never intended to be there forever, and at the five-year mark, that was starting to look like the case.

Leaving wasn't easy. I liked Amazon. The culture. The people. The [leadership principles](https://www.amazon.jobs/principles). The autonomy. The compensation.

But I had an idea of a thing I wanted to build and a hunger to see if I could make it on my own. And so I left.

## Making Thence

I suppose there is some irony that the first thing I did after leaving AWS was to start a consulting business around it. It felt like the right way to dip my toes into the waters of ~~unemployment~~ startup life. I was very much still passionate about the platform, I had deep expertise in how it worked, and there was a great market need for it.

I won't go into detail about incorporating the business other than to say that I did and incorporated as Thence LLC, a limited liability corporation based in Washington.

After incorporating, I was ready for clients but didn't have the first notion of how to go about it so I studied and read up on the subject. I followed internet people like [Patrick McKenzie][8217-0001] and [Eric Dietrich][8217-0002]. I read business books like [Getting Naked][8217-0003] and [The Blue Ocean Strategy][8217-0004]. And I reached out to folks like [Corey Quinn][8217-0005] and [Rich Uhl](https://www.1strategy.com/about-us/) who were already doing the sort of things I aspired to do and were kind enough to give me the time of day.

From this research, I took away some of the following:

- it's important to find a niche to specialize in, otherwise, you risk being a commodity
- avoid online agencies like [Upwork](https://www.upwork.com/) as they tend to commoditize your skills
- build a brand through channels that you control (e.g. email lists, blogs, etc.)
- always charge more than what you feel comfortable with (a good mark to shoot for is 2x more than your normal hourly rate)

## Finding a Niche

Finding a niche makes sense on paper but it's hard to be picky when you're just starting. When I started, I did any work on any cloud for anyone.

My first gig as a consultant was with an IoT manufacturer running on Azure. While the job was successful, I quickly realized that I spent too much time learning Azure-specific quirks (and wasn't leveraging my knowledge of AWS specific quirks). And so I decided to focus solely on AWS jobs afterwards.

What followed was a series of jobs across the stack, from coming up with high-level service architectures to diagnosing database-specific connectivity issues. With each job, I would gather feedback from my clients and myself, with the goal of figuring out my specialty.

I tried finding something I enjoyed, something I was good at, and something the market needed. The ideal specialty would be at the intersection of all three things.

Below is a list of "specializations" I experimented with during the year.

### Scaling

I started my career at AWS working on scaling [Elastic Load Balancers](https://aws.amazon.com/elasticloadbalancing/) and built three successive generations of scaling systems for the service. This was work that I enjoyed and wanted to do more of.

Unfortunately, I had a hard time finding clients for scaling. The sort of startups I was meeting early on did not need it and enterprise had teams dedicated to it. As I became more established, I did get a few scaling audit related jobs but they were few and far between.

### Cost Optimization

At AWS, I was one of the main architects behind the [Load Balancer Capacity Unit](https://aws.amazon.com/elasticloadbalancing/pricing/) (LCU). This was a custom metric derived from other system metrics and represented the "work" the load balancer did. Subsequently, all load balancers would be billed according to this metric, which meant we had to triple check all our benchmarks to make sure this new billing model would be cheaper than the old one (which billed by bandwidth). This exposed me firsthand to the complexities of AWS billing and made me realize how it was as much an engineering problem as a financial one. Billing in AWS is tied directly to configuration and reducing the bill means understanding both the financial and engineering tradeoffs of a particular configuration.

As a consultant, cost optimization turned out to be an easy thing to pitch — after all, “I want to pay **more** for AWS” is not a thing anyone has ever said. My offer was a guaranteed minimum of 30% (usually closer to 60%) off your monthly bill in exchange for a percentage of your total spend on AWS (as a consequence, this only made sense for clients that had a minimum monthly spend of 5k/mo or more).

### HIPAA

Near the middle of my AWS tenure, I switched teams to become the first hire on a skunkworks project that eventually spun out into [Amazon Comprehend Medical](https://aws.amazon.com/comprehend/medical/). I build much of the initial ML infrastructure in the first year and learned all about [HIPAA](https://www.hhs.gov/hipaa/for-professionals/index.html) and what it means to satisfy it from a technical standpoint.

I ended up doing a series of jobs in healthcare, most notably architecting the entire AWS stack for an early healthcare startup on the east coast. This proved to be a lucrative niche since the intersection of people that could do AWS and were familiar with HIPAA was quite small.

### Serverless

[Serverless](https://en.wikipedia.org/wiki/Serverless_computing) was not something I worked on directly at AWS but it was something I followed closely. Just like cloud computing, (I believe) the serverless paradigm will revolutionize how people build digital services. I spent part of last year both [[writing|blog.aws.2019-02-04-lambda_cold_start_idle]] and [[speaking|blog.aws.2019-02-28-building_sls_apps]] about serverless as well as [building](http://backuptable.com/) a few services on top of it.

In my consulting, I find that the majority of startups have already adopted serverless or are moving towards it. More often than not, I find my job to be one of cautioning my clients against using serverless for applications it is not (yet) suited for. I'm excited to continue working with serverless and see this space grow in 2020.

### DevOps and CDK

As someone used to writing/generating thousand-line json based cloudformation templates, the introduction of the [CDK](https://docs.aws.amazon.com/cdk/latest/guide/home.html) in 2018 was like finding an oasis after years wandering the desert. For the first time, [Infrastructure as Code](https://en.wikipedia.org/wiki/Infrastructure_as_code) had an implementation that was not a config or a config-like language but actual code. You can find my initial ~~evangelizing~~ writing about the CDK [[here|blog.aws.2019-06-12-cdk_all_the_things]].

In my consulting, I use the CDK  to deploy infrastructure for my clients when at all possible, because it makes my job easier. What I didn't expect was for clients to reach out to me specifically because of CDK and wanting to integrate CDK into their production pipeline. I ended up publishing a [CDK best practices guide](https://github.com/kevinslin/open-cdk) based off my work and look forward to continued work in this space this year.

### Education

I like learning and teaching what I've learned. To that end, AWS is like a fantastical candy shop where the more you consume, the more there is left. I write and give talks to help distill, consolidate and share what I know with others.

In 2019, I did a small number of on-site training sessions and am open to doing more in 2020.

### Niche?

So at the top of the article, I talked about specializing. My journey of specializing as a consultant went as follows:

- everything on all clouds
- everything on AWS
- a list of very specific things on AWS

Despite this being a small subset of where I started, one could make the case that this is still too many things to count as a specialty.

I interviewed [Charlie Bell](https://www.linkedin.com/in/charlie-bell-05446835), the SVP of AWS, many years ago as part of an internal podcast I hosted at Amazon. I asked him how he decides what the company should focus on, and Charlie replied that his job was to "say yes to as many things as possible."

Left to my own devices, that tends to be my answer as well. I realize that this is easier for a Fortune 500 company to do than an individual formerly employed at a Fortune 500 company. That's why I will continue to whittle down this list in 2020 (though I suspect the end result will still be more than one thing).

## Establishing a Pipeline

Finding clients is something that every freelancer struggles with, me included. What I've learned is that getting a client is, at the end of the day, about establishing trust (this is the answer to most things involving humans).

Establishing a client pipeline is finding a scalable means of signaling trust. Like all interesting things in life, there's no cut and dry way of establishing trust but I'll go over some of the things I did.

### Networking

Of all channels I've tried, I found the personal network to have the highest signal to noise ratio. Especially when first starting out, these sorts of introductions can get you into places you otherwise have no business being. The problem is that this network stays mostly static unless you make an intentional effort to grow it.

In 2019, I went from never networking (besides the sort that involved packets) to spending half my time at meetups, conferences and user groups (basically attending anything with "AWS" in the name).

My approach to talking to strangers was to not sell but listen. Figure out what brought them to the event. What they were doing on AWS. If they were encountering problems, offer solutions. It was only at the point when they asked me what I did that I talked about consulting.

I found this approach to not only be highly effective in getting clients but to also get me over my "moral aversion" to "selling." It goes back to building trust — I found if I could start there, the rest would follow.

My other networking trick is to use [airtable](https://airtable.com/) to meticulously track every person I’ve met and every interaction I've had. For each contact, I track each interaction, what was discussed and the outcome. I also attach the desired frequency of communication with each lead, list problems they had and the number of days since the last follow-up. This tracking has also spilled over to my personal life, as I started tracking my interaction with friends on a similar table.

### Agencies

While networking brought me my initial set of clients, the process was also a crapshoot — for every fifty people I talked to, I might produce one lead. This led me to explore various freelancing agencies like Upwork, Toptal, and Facet.

I think of networking like dating in real life — you go to different venues to strike up conversations but have no idea if the person is even looking for relationships or is already in one. By this analogy, agencies are like dating sites — they are platforms where at least you know everyone is actively looking for the same things you are.

[Upwork](https://www.upwork.com/) is the Amazon of talent agencies. They have the biggest selection of jobs and freelancers and you go on Upwork because you want the lowest price. Most consulting practitioners counsel folks to [stay off the platform](https://www.youtube.com/watch?v=PNayKRir14U&feature=youtu.be&t=142) for that reason.

Starting out, I didn't see the harm in trying. While the initial jobs did not pay much, it was very easy to find clients and get references. And once you had some history, clients could find you organically when looking for freelancers.

I found that the discoverability aspect of Upwork made it good for niche specializations (e.g. HIPAA) and I would get a few inbound leads from Upwork every month in this manner. For these jobs, I ended up charging \$300/h. While this matched my regular rate, it was more than double the average Upwork rate. I wanted to experiment if Upwork could support higher tier freelancers and with only a few exceptions, I found that it could. Inbound leads make up 100% of my jobs on Upwork at this point.

[Toptal](https://www.toptal.com) I had more trouble with. They advertise themselves as the top 3% of freelancers and screen out people through a multi-part interview process that includes a lengthy take-home project. The problem is that after being let in. there is no possibility of "inbound client requests." All jobs are posted by Toptal recruiters and they control both sides of the conversation until you agree to a job. The pay was not much more than what you could find on a typical Upwork job and the availability of jobs was far less. I ended up not doing anything on Toptal.

Finally, I tried [Facet](https://www.facetdev.com/). This was another take on the agency model, but one comprised exclusively of former FANG (Facebook, Amazon, Netflix, and Google) employees (today it’s FANG and FANG like companies). Onboarding was virtually painless (at least at the time I did it in early 2019) and required no additional interviews. Speaking of interviews, I did [an interview](http://folkstories.org/16) with the founder of Facet and we talk about the company at length if you’re curious for additional details.

Facet has the freelancer set their own rates and charges 20% on top of that to employers hiring off the network. This is different from Toptal and Upwork which also have a 20% fee but charge it to the freelancer. Facet’s model usually works out to higher take-home pay and jobs on the network generally start off with much higher hourly rates. My biggest problem with Facet is that almost all the opportunities are full time, which was explicitly what I was not in the market for. Also, like Toptal, they brokered both sides of the transaction which meant no possibility for direct inbound contact with potential clients. I ended up not doing anything on Facet either.

### Platforms and Branding

I left Amazon to control my own destiny. While agencies like Upwork and Toptal help you build credibility from reviews, and platforms like Medium and LinkedIn have reach, you are, at the end of the day, playing in someone else's sandbox (and at their mercy). Note that I'm not against leveraging those platforms (I do it a lot) but it's important to me that I’m in complete control of my primary channel(s) — hence this blog.

Blogging, and more broadly speaking brand building, is a means to build trust. It’s a public inventory of prior thoughts and work. Of all the channels mentioned thus far, it is definitely the hardest to build and maintain but like the humble index fund, has the biggest payoffs over longer periods of time.

In 2019, I published eight AWS-related posts on my blog. [[Benchmarking Lambda's Idle Timeout Before A Cold Start|blog.aws.2019-02-04-lambda_cold_start_idle]], [[A Tale of Two Buckets|blog.aws.2019-07-17-tale_of_two_buckets]], and [[CDK All The Things: A Whirlwind Tour|blog.aws.2019-06-12-cdk_all_the_things]] were some of my more popular posts in 2019 and correspond to the themes I like to write about (serverless, security, and CDK).

I have a love/hate relationship with writing — like running marathons, it's always something I'm glad I did in retrospect even when the process itself can be agonizing. While AWS lends itself to an endless amount of things to write about, translating my thoughts into words is a slog that takes me eight hours on average from beginning to end. Time is something I'm especially cognizant of since every minute spent writing is at the opportunity cost of paying work. Thus, I don't publish frequently and only when I feel I have something novel to say.

While there’s no guarantee of any immediate “payoff” after publishing a piece, writing plants the seeds for future opportunities. Some of my biggest clients have come from things I published. This includes a consulting gig with my former employer — AWS.

And writing doesn't always have to be an eight-hour slog. In November, I experimented with short-form publication in the form of once-a-day tweets.

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">in celebration of the 30 days leading up to re:invent, i&#39;m going to be doing an aws tip a day, advent calendar style. tips will cover security, cost, scaling, availability, <a href="https://twitter.com/hashtag/awscdk?src=hash&amp;ref_src=twsrc%5Etfw">#awscdk</a>, and horrible nasty soul sucking edge cases everyone should watch out for<a href="https://twitter.com/hashtag/AWS?src=hash&amp;ref_src=twsrc%5Etfw">#AWS</a></p>&mdash; Kevin S Lin (@kevins8) <a href="https://twitter.com/kevins8/status/1190337312527249408?ref_src=twsrc%5Etfw">November 1, 2019</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

The tweets were received much better than I anticipated and were fun to write. I will probably do more of these in 2020.

### The best channel

At the end of the day, just like with specialization, there wasn't one single channel that I focused on. I tried everything and they all worked to varying degrees.

Networking and blogging took the most time and had the least certainty but also had much greater upsides when they eventually did come through. Going through agencies gave the most immediate results but resulted in less pay and dependence on a platform.

Today, all my leads are inbound — either through a referral or from someone who has read something I've published. For 2020, the plan is to continue writing about things that interest me and plant more seeds.

## Growth and Introspection

By late July, I found that the consulting business had been way more successful than I anticipated. I filled my hours, was making up the gap in the lost Amazon salary, and was actively turning down interesting jobs due to time constraints.

At this time, I was thinking about scaling up and interviewing people to expand my consulting group. It was in the middle of the process that I talked to another AWS consultant who had hired people in the past but was now going solo. She mentioned how hiring people was like taking venture money — it would commit you down a path of always needing more work to pay for the talent you had on file.

After the conversation, I took some time to reflect on whether this was what I wanted. My motivation with consulting was to create a revenue stream to ease the psychological toll of doing something that might take a great deal of money to bootstrap. It was never meant as a full-time job. I took a few weeks to think it over and ultimately decided against scaling up the consulting business.

I left Amazon because there was something I wanted to build. Consulting was a means and I didn't want it to become an end. And because of that, I started weaning down my regular consulting to twenty hours a week so that I could spend time building the thing I left Amazon to build.

## Baby Steps

But before I could build the thing, I built an intermediary thing. I still wasn’t quite sure how to build or monetize what I wanted to build and wanted an early win by shipping a service with more immediate returns.

And so I took a two-month detour to built [Backuptable](https://backuptable.com), a SaSS service that does backups of Airtable.

I mentioned earlier in this article that I use Airtable extensively both personally and professionally. It has no native backup solution, despite that being one of the most requested features in [user forums](https://community.airtable.com/t/offline-local-backup/754). My policy for startups (and SaSS in general) is to have backups of all my data so I had already built a solution for myself.

It took me a little over a month to finish building the SaSS wrapper around my solution. I launched it on [product hunt](https://www.producthunt.com/posts/backuptable) with little fanfare and was pleasantly surprised to find positive feedback and paying customers from the first week. I then spent the next month stabilizing the platform so that it more or less runs without needing any further attention from me.

After launching, I got a lot of requests for additional features from customers that were willing to pay more for said features, and I was torn between implementing new features versus keeping the project stable. I ultimately decided to forego any further work on the platform outside of bug fixes — Backuptable, like consulting, was a means to an end and I didn't want to mix up the two (again). And now with a “successful” service release, I would finally start working on the thing I left Amazon to build.

## Dendron

Starting in late September 2019, I began working on a service to solve a problem that has been on my mind for much of the last decade. This problem is best described by Vannavear Bush, an early visionary in information science.

> "We are overwhelmed with information and we don't have the tools to properly index and filter through it. [The development of these tools, which] will give society access to and command over the inherited knowledge of the ages [should] be the first objective of our scientist."
>
> --- Vannavear Bush

The quote is from an essay Bush published in **1945** and it is no less true today. While we've made drastic increases in the amount of information we can put into systems and knowledge bases, getting that information back out remains an unsolved problem.

All knowledge bases today (e.g. Evernote, Confluence, Notion, etc.) of any sufficient scale ultimately rely on search as the primary interface to "index and filter" information. The problem is that search relies on techniques like keyword frequency and more often than not either returns too many results or none at all. It also relies on a human to scan pages of results. This problem gets worse the more data you have, and makes entering new information like investing in penny stock where the chances of getting back what you put in are quite slim.

I needed a tool that would ensure anything I indexed I could find again, even if it was several years after the fact. I needed something that could return results instantly without searching or scanning. I needed not just the information I was looking for but also the context around it. I needed something that would work with the 10k+ plaintext markdown notes that I already had.

Based on these needs, I created [Dendron](https://dendron.so/) — a tool that let me instantly reference all the information I cared about.

Dendron can be best described as a taxonomy engine — it allows users to create taxonomies for the information that they care about and helps index and retrieve information via the given taxonomy. It requires much more upfront work from the user’s side to craft and manage their taxonomy, but the payoff is being able to finally make full use of one's knowledge base.

Dendron is currently in limited early access for customers that have text-based knowledge bases stored in source control (e.g. Github, Bitbucket, Gitlab, etc.). If you are interested in early access for yourself or your company or want to be notified when we become generally available, please sign up [here](https://kevinlin147106.typeform.com/to/wQU2Cw). Otherwise, if you have additional comments or are simply passionate about organizing information, the best way to reach me is at kevin(at)thence(dot)io.

## Next

I realize that my strategy is a little unorthodox — the common startup advice is to pivot to a new project when you don't find product market fit with your current one. My pattern this year has been pivoting away from profitable businesses to focus on a project whose fit is unproven. Business wise, it makes no sense. I can only explain it as a deep yearning I feel for something like this to exist in the world and an undeniable impulse to make it so.

Looking ahead at 2020, I'm looking to double down on my niche(s). I will continue to consult but with a hard cap on my weekly hours. Backuptable will continue to exist and backup tables but otherwise remain as it is. Most of my focus will be spent working on Dendron and promoting a search-less paradigm for finding information. There will be a public release of Dendron early February with specific AWS-related integrations, and I will write more about that soon.

Looking back, I’ve worked harder and longer in 2019 than I’ve ever done in my life. While the scale of the systems I work on is smaller than what I did at Amazon, the stakes (for me) feel much bigger. Everything I do directly affects my bottom line and every action I take is ultimately because of a decision I’ve made and am responsible for. This direct ownership of my work has been extremely satisfying and stressful at the same time. 2019 was a rollercoaster of exquisite highs and crushing lows, the details of which I will leave to a future post. Ultimately, I don’t know where the startup path will lead but I’m determined to follow it wherever it goes. It’s about the journey, and this one is just beginning.

[8217-0001]: https://www.kalzumeus.com/
[8217-0002]: https://daedtech.com/
[8217-0003]: http://www.amazon.com/dp/product/0787976393/ref=as_li_ss_tl?ie=UTF8&linkCode=ll1&tag=bretttercom-20
[8217-0004]: https://amzn.to/2QsQAym
[8217-0005]: https://twitter.com/QuinnyPig
