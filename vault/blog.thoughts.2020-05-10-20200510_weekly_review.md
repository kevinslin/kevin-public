---
id: a05c5571-504f-4581-a5fe-50589c0f2bd4
title: '2020-05-10: Weekly Review'
desc: ''
updated: 1627079234773
created: 1596518032933
layout: single
date: '2020-05-10'
categories: thoughts
category: thoughts
header:
  teaser: /assets/images/conv-weekly_review.jpg
toc: true
toc_sticky: true
excerpt: 'Structured Knowledge Management, Retrospectives and Using (aw)less'
---


!["Weekly Review"](https://kevinslin-images.s3.us-west-2.amazonaws.com/images/conv-weekly_review.jpg)

# Preamble

Most of the articles this week are on the topic of structured knowledge management. Perhaps not the biggest shocker since I'm both working on a [startup](http://dendron.so/) to help people manage their information as well as taking an [online course](http://fortelabs.co/) on that very topic. Just like parallel computing, I think our ability to effectively apply the information that is available could be improved by many orders of magnitude using the right tools and systems.

# Structure

- Thoughts: What I'm thinking about based on articles I'm reading and things I'm working on
- Systems: Tools and workflows I'm experimenting with to more effectively learn and get things done
- Outputs: Things I'm working on in various stages of completion

# Thoughts

- Structured Knowledge Management
  - Articles
    - [🌱 My blog is a digital garden, not a blog](https://www.notion.so/kevinslin/My-blog-is-a-digital-garden-not-a-blog-fe9d0119cb334e0a9d21c17f2e94297d)
      > Using this newest-first approach makes the site impenetrable and less useful for me and especially for anybody that might be visiting to learn or research
    - [How to Make A Memex - Sarah Constantin](https://www.notion.so/kevinslin/How-to-Make-A-Memex-Sarah-Constantin-66357fa6dbf84301ba0660ccaf8c57dc)
      > Social media has a short memory. The defaults don't permit you to organize your own space.
    - [Principle 5: Conform to the patterns and expectations of the genre and schemas | I'd Rather Be Writing](https://www.notion.so/kevinslin/Principle-5-Conform-to-the-patterns-and-expectations-of-the-genre-and-schemas-I-d-Rather-Be-Writi-c99c796cec9d4666a352c58430216ae6)
      > A schema is a cognitive framework or concept that helps organize and interpret information. Schemas can be useful because they allow us to take shortcuts in interpreting the vast amount of information that is available in our environment. (What Is a Schema in Psychology?)
    - [John Locke’s Method for Common-Place Books (1685) – The Public Domain Review](https://www.notion.so/kevinslin/John-Locke-s-Method-for-Common-Place-Books-1685-The-Public-Domain-Review-77c03aeef67b4be294c68f13ae799fc8)
      > In general, the commonplace book would result in a wonderfully tangled mixture of reading and writing, where disparate ideas could be fruitfully thrown together onto the same pages, fixed together only by a formal method (and of course similar word roots).
  - Notes
      - Structuring information in a way that is easy to capture, reference and make connections from is hard. With physical constraints (eg. books and paper), people had to get creative to index and compress all that they cared about. In today's world of digital abundance, both from an information and tools perspective, people seem to have all but given up on trying to "manage information". Linear newsfeeds are great for dopamine hits and scrolling addiction but are hard to make use of when the moment has passed. But it doesn't [[need to be this way|blog.pkm.2020-03-30-its_not_you_its_your_knowledge_base]].

- When the status quo is "good enough"
  - Articles
    - [Sunsetting Documentation - Meta Stack Overflow](https://www.notion.so/kevinslin/Sunsetting-Documentation-Meta-Stack-Overflow-f9dbb72cf128469eba181741313119d6)
      > Stack Overflow is already good enough at providing documentation of obscure features. Even when considering just the company's mission of helping programmers “learn, share their knowledge and build their careers”, Documentation isn’t the most efficient use of resources.
  - Notes
    - Post-mortem of the stack overflow documentation effort. Was following it closely when it launched but like a lot of people mentioned in the retrospective, ultimately didn't use it. Existing documentation was "good enough" and when it wasn't, regular Stack Overflow worked. This one hit close to home because I did a similar post-mortem for the [initial preview of Dendron](https://aws.dendron.so/). At that time, it was focused on consolidating all AWS documentation in one place with a small consistent hierarchy. It suffered from much of the same problems.

# Systems
<!-- [[dendron://notes/awsless]] -->
- Using `awless` as a replacement for [aws-cli](https://aws.amazon.com/cli/):
  - Notes
    - I can never do anything on the AWS CLI without looking it up on the [AWS CLI Reference page](https://docs.aws.amazon.com/cli/latest/reference/index.html#cli-aws). `awless` makes it more manageable by reducing most commonly used commands to a small hierarchical set with sane defaults.
      <blockquote class="twitter-tweet"><p lang="en" dir="ltr">been using <a href="https://t.co/bYbwX9ycdV">https://t.co/bYbwX9ycdV</a> for the past week. it&#39;s a CLI for managing <a href="https://twitter.com/hashtag/aws?src=hash&amp;ref_src=twsrc%5Etfw">#aws</a>. enables easy ad-hoc inspection/prototypes without going into the console. favorite features: small and hierarchical set of commands to manage resources and references resources by name</p>&mdash; Kevin S Lin (@kevins8) <a href="https://twitter.com/kevins8/status/1259264023406587904?ref_src=twsrc%5Etfw">May 9, 2020</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>
- Using a project completion checklist
  - Notes
    - Got the idea from Tiago of [fortelabs](https://fortelabs.co/). Basically a checklist to do at the end of a project. My own checklist below as reference (it's pretty basic).

    ```
    - [ ] mark out in task manager (rtm)
    - [ ] review and consolidate project notes
    - [ ] move projects into archives
    - [ ] update related areas
    ```

# Output

- [Dendron](http://dendron.so/)
    - Integrate [outline editor](https://github.com/outline/rich-markdown-editor) into product
    - 6x Customer Interviews
- [Thence Consulting](https://thence.io/)
    - Automate provisioning/bootstrapping of a bunch of services
    - Figuring out if there was a better way of transfering 50GB of data from EBS to EFS besides rsync (there wasn't unless you count parallel rsync)
      <blockquote class="twitter-tweet"><p lang="en" dir="ltr">anyone know if there is a better way to transfer 50gb from an EBS volume to EFS than mounting them on the same EC2 instance and doing rsync? <a href="https://twitter.com/hashtag/aws?src=hash&amp;ref_src=twsrc%5Etfw">#aws</a> <a href="https://twitter.com/hashtag/ebs?src=hash&amp;ref_src=twsrc%5Etfw">#ebs</a> 🤔</p>&mdash; Kevin S Lin (@kevins8) <a href="https://twitter.com/kevins8/status/1258157439121154048?ref_src=twsrc%5Etfw">May 6, 2020</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>


