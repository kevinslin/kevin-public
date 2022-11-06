---
id: c800f3c4-7339-4a2a-b684-c847676e85c4
title: Why I Choose Vim
desc: ''
updated: 1596518032925
created: 1596518032925
tags:
  - thoughts
  - tools
  - dev
status: publish
published: true
layout: single
date: '2015-09-25'
---

Helen of Troy might have had a face that launched a thousand ships but the
Trojan war pales in comparison to the [flame
wars](https://en.wikipedia.org/wiki/Editor_war) developer wage today over
their favorite editor.

As a developer, a lot of time is spent inside an editor writing code.
Editors have changed much in the past fifty years, evolving from simple
[line editors](https://en.wikipedia.org/wiki/Line_editor) like qed to rich
[Integrated development
environments](https://en.wikipedia.org/wiki/Integrated_development_environment)
(IDE). IDE's bear as much resemblance to simple text editors as iPhone's do
to flip phones.

The accumulation of features was spurred not just by better technology but
also by the increasing complexity of both programming languages and
programs written in said languages. The Java programming language is so
verbose that writing it outside an IDE (or at least with auto completion)
classifies as masochism of the first order. And as far as programs go,
whereas  the original version of Unix(1.0) was written with ~15,000 lines
of code (LOC), the windows vista operating system is over 50 million LOC.

Today's technologies are many orders of magnitude more complex than what
came before - the editor is where developers go to battle this complexity.
Like a Nascar race car, every setting needs to be tuned to a fine hairs
precision. Spending time in an editor is like spending time
with a loved one - it requires commitment and deep understanding of the
other to make it work.

This writer has chosen [Vim](https://github.com/vim/vim) as his editor.

Vim was created by Bram Moolenaar during the start of the 90's as a major
upgrade to the vi editor (Vim stands for vi improved). Vim is a simple
line based editor that is available by default on all Unix based operating
systems and works on all platforms. It is a modal text editor infamous for
its steep learning curve.

In typical text editors, one can just start typing and have key presses
map directly to the characters appearing on screen. In Vim, one needs to
first enter "insert mode" before this happens. Vim by default starts in
normal mode where each key press maps instead to a command. There is
a third mode known as visual mode which applies commands to highlighted
blocks of text.

Vim is extremely powerful once you get your head around its modal editing
modes (and endless key mappings) but the real icing of this metaphorical
cake is the inexhaustible number of ways it can be extended. This is done
via Vimscript, a custom scripting language that ships with Vim which gives
developers control of every aspect of the editor, from changing the
background color to custom commands that activate only if the current word
under the cursor matches a specific regex. The way I like to think of
Vimscript is as a meta programming language to the coding process.

Meta programming is writing a program that is able to treat other programs
as data, giving the developer the power to change the very laws of said
programming language. This is a very powerful feature (just ask a lisper);
in the real world, you can think of this as being able to change the laws
of physics at whim. Want to explore other galaxies? Simply enable the
"faster-than-speed-of-light" travel option and it's done.  Popular web
frameworks like rails and django make extensive use of meta programming to
radically simplify the web development process.

Coming back to Vimscript, if you think of an editor as the world in which
code is crafted, Vimscript is the means to change the laws of said world.
And that is precisely the case today as Vim plugins which enable fuzzy
file search, multiple cursors and intelligent auto completion bring this
over two decade old editor on par with the newest IDEs of the day.

If we step out one abstraction level and see editors like Vim as a tool to
extend human capabilities, Vimscript becomes a means of continual tool
refinement. You can create an incredibly powerful feedback loop when you
can continuously improve the tools that you use and in fact, use the very
tool you're refining to make the next version of said tool.

And at the end of the day, building better tools is not just a fun past
time but something essential to the continual evolution of the human race.
Humans are completely dependent on the tools we make. This was true in the
past when we worked in groups to take down large predators with spears and
knifes and it's true today where every aspect of our lives, from banks to
hospitals, is managed by the tools we have build.

Better tools are the rising tide that raises all ships - by ship I mean
the collective capabilities of the human race. The popular proverb "a bad
workman always blames his tools" is true to an extent but I would argue
that a workman who doesn't invest in their tooling might deserve the
blame. The world today is moving in a trend of ever increasing complexity.
Gone are the days of using electrical tape and solder to fix household
electronics; these devices have evolved from simple one way radios
to smart phones that require specialized screws just to pop open the
casing and electron microscopes to see the circuits. The only way we can
reign in this complexity is to do what we've been doing since the dawn of
our species - create better tools. My tool of choice in this eternal
struggle is Vim.

