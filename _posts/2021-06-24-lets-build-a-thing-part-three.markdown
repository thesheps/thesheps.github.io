---
layout: post
title: "Let's Build a Thing! (Part Three)"
date: 2019-06-24 08:44:00
comments: true
categories: js, creativity, front-end, hack, typescript, react, vue, web components
---

Welcome back! As I seem to have found my writing cadence I thought I'd keep on _ploughin' on_ with
this series of build-a-thing posts. You can find the previous posts
[here](https://medium.com/@thesheps/lets-build-a-thing-part-one-7b29e4f8e955) and
[here](https://medium.com/@thesheps/lets-build-a-thing-part-two-64b74940f91a). To re-cap, I've:

- Configured my local dev environment for TypeScript.

- Devved a really simple implementation of the [Observer Pattern](https://en.wikipedia.org/wiki/Observer_pattern)

<br />

With these bits and bobs in place I've got a really simple way of notifying components when
anything changes in my state. The _really_ interesting bit comes next. I now want to build a
concrete `Observer` implementation which is able to traverse and update the DOM when the state
is modified. LET US WRITE A TEST.

<script src="https://gist.github.com/thesheps/e68b45aedffec10a27b767ed2572d227.js"></script>

In this gist I've got a teeny-tiny class called `Toy`. Its responsibility now is simply to
register itself as an observer with whatever controller it is "newed up" with. Nice - we're now
able to subscribe to changes in the state of our `Controller`, and can use it to update the
**Document Object Model!** So what kind of functionality do we want to add to this new class?
Initially I'd like to support the rendering out of _double moustaches_ in a given document with
whatever contents are represented in the state of my `Controller`. EG:

```html
<div>
  {{ test }}
</div>
```

Becomes:

```html
<div>
  Hello, World!
</div>
```

Why double-moustaches? Because there may be legitimate reasons why consumers of my lovely library
want to wrap pieces of text in single moustaches. Let's not cause them a headache! So it looks
like there's another simple piece of functionality here that we can drive out in a couple of other
tests! Let's do that thing:

<script src="https://gist.github.com/thesheps/33d93c2f1190fbc87664e2f491fed75d.js"></script>

I've updated my constructor to accept an additional parameter - The `HTMLElement` to render.
The `update` implementation of my Toy class now uses this "Root" element to find and replace any
double-taches with the correctly-rendered content (as defined in the `state` of the Controller
class). I've _also_ updated the Controller's `registerObserver` function such that it
automatically updates any newly-registered Observers. This means that all Observers get an
up-to-date copy of the Controller's state when they are registered.

But what happens on a subsequent re-render? Let's write another test and see!

<center>
    <img src="{{site.url}}/assets/posts/2019-06-24/failing-test.png">
</center>

So this is failing for a pretty good reason - As soon as I've un-moustached the DOM I've lost
the context of where the original moustaches _were_. Any subsequent un-moustachification is going
to fail because the moustaches are no longer there :pensive: - Let's fix this!

<script src="https://gist.github.com/thesheps/387db65690e04e92cda8408236ae96a2.js"></script>

The latest version of the Toy class simply creates a deep clone of the original HTMLElement, and
uses _that_ to find whatever moustaches are needed to be **ELIMINATED** from the DOM. This is an
extremely naïve implementation of dynamic DOM rendering, but is working well for this latest test
case!
