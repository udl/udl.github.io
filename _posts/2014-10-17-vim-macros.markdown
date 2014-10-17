---
layout: post
title:  "Vim macros"
date:   2014-10-17 09:39:10
categories: vim, editor
---

Let's face it - [vim](http://www.vim.org/) is awesome!
If you don't think so, you can stop reading. Because today's post is
on a useful little feature called macro.

I usually use macros whenever I have to make changes that are hard to do
with regular expressions. And it's that simple:

- Hit **q** in the normal mode followed by the name of the macro, for
  example **a**
- Do whatever you would like the macro to do, for example go to the
  beginning of the line, add a word, jump over the next word and add
  XYZ
- In normal mode type **q** again - the macro is now stored

**@a** replays the macro.
If you want to replay it more than once, type **\<nr of times\>@a** as with every
other vim command (for example "20@a").

Happy viming!
