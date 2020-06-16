---
title: "Let's Build a Thing! (Part Four)"
date: 2019-06-26 08:21:00
comments: true
categories:
  - js
  - creativity
  - front-end
  - hack
  - typescript
  - react
  - vue
  - web components
---

Well, good morning :wave: - I hope that you've been enjoying the exploits over these last few
posts. I'm casually aware that there are a _lot_ of similar articles out there discussing the
hand-rolling of front-end frameworks, and that what I'm doing here may be no different. That
being said I've enjoyed the writing of it, so if at least one other person has found this
useful/entertaining then I'm a _reasonably_ happy chappy!

I've also subsequently been informed that I'd make a **shit** BA, and that there's no statement
of value anywhere in my user stories :cry:

<center>
  <img src="{{site.url}}/assets/posts/2019-06-26/trello.png">
</center>

So there!!

I've been doing some work on this Toy framework over the last couple of days, and have finally
managed to get it into a state whereby a child hierarchy of HTMLElements can be rendered
sucessfully! My final `Toy` class looks like this:

<script src="https://gist.github.com/thesheps/443d467ae8c002ddee4df1283eae72a1.js"></script>

Simple, eh??? The main challenge that I had around the rendering of child elements was that each
child node needed a way to be rendered from its original template representation. The cheapest
(see: least complicated) way I found to do this was simply to _annotate_ each child node with a
new property called **template.** This node is always going to contain an original, moustachiod
version of the `HTMLElement` We can now just swap out the `TextContent` of any Node with its
rendered equivalent. Great!

The only remaining complexity with this approach is that whenever the text content is replaced,
so too is the underlying _Text_ node's content. This basically meant that whenever a parent is
rendered, _all child pending changes are overwritten_ :confounded: To get over this final hurdle,
I just needed to render out the `firstChild` of each node, and have some crazy recursive
algorithm to ensure that every Child node of every parent node is descended and rendered
(#descrendered).

<br>

## But what about performance, eh?

Bloody great question, that. Rumour has it this sort of _BRUTE FORCE TRAVERSAL_ is actually
very bad for the soul. Not only that, but somewhere there's an ancient, crumbling laptop that's
hurriedly whirring itself into life on the off-chance it has to run this goddawful excuse for
performant JavaScript sometime in the next 10 years. Let's see what the damage is, I guess? And
if it's truly shite we can try and re-write some of it!

A pretty short summation this time folks, but I wanted to get these out and draw a line under
_what has gone before_ and _what is yet to come!_

Byeeee.
