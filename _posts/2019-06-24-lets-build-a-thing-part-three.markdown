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

Why doubly-moustachiod? Because there may be legitimate reasons why consumers of my lovely library
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
case. Just one thing, though - We've not actually "wired" this code up to an actual HTML page,
yet. It is all conjecture, whimsy and caprice. Let's see about getting this sorted, as the old
adage goes:

<br />

# Integrate Early and Often

All I want to do here is serve a simple Index page, and have it construct an instance of Toy,
using a script tag. This, too we can drive out with tests! The below gist shows the overall
approach, and involves a couple of new **npm** dependencies, namely `supertest` and `express`.

<script src="https://gist.github.com/thesheps/8cc467645411965508fe068d970dbe5d.js"></script>

There are quite a few amendments to support this in the above Gist, so let's take them all
individually.

## app.js

This code file sets up an extremely simple web app using express. It serves everything under the
_dist_ path as static assets, and serves the file `index.html` on the default route.

## server.js

Here we just bootstrap the Express server and serve the above app on port 5678

## index.html

This is a simple skeletal html file with a single script reference which points to the transpiled
toy.js which is produced by Webpack, using the ts-loader. Notice how the script is the last thing
in the page? That's pretty important...

## package.json

Here I'm just pointing out the brand new, swanky `serve` task which is going to both transpile
all of the tasty TypeScript into a single, transpiled artifact.

## server.test.js

Here we just establish that Express is functioning correctly. That we are serving a vanilla HTML
page with a successful HTTP status code. We can continue to flesh this test out shortly, but for
now this is the minimum amount of stuff I want to assert on, quite frankly!

## index.ts

This is the entry point for the application! It includes 3 exports. **Toy**, **Controller** and
**MockController**. We're going to use MockController to bootstrap this trivial example, but
we'll make sure to exclude it from the app going forwards!

## webpack.config.js

**This is dead important!!** As the context of toy-js is to be run in the browser, it's extremely
important that the _library_ option here is configured. This has the effect of **namespacing** the
transpiled JavaScript so that it is available in the browser. This, coupled with the fact that
all of the TypeScript classes need to have a _named export_ is the key to making the library
available from a web browser. This took me longer than I care to admit to figure out...

<br>

<center>
  <img src="{{site.url}}/assets/posts/2019-06-24/et-voila.png">
</center>

<br>

Et voila! I know there's been a bazillion things that I've explored in this post, but it largely
boils down to a few main things. I've created a simple Toy class which is able to parse and
manipulate the DOM. We've then given a little effort to creating a _dead simple_ test harness.
We've learned a little more about our Webpack configuration, and specified a new default export
file which is used to export all the types we might need in the final, transpiled distribution.

My respect for the engineers behind React, Vue and other frameworks is growing exponentially! Next
time I'll look at extending the tests here to include some performance feedback. How long does
this thing take to render a simple parent div? What about child components?

IT'S ALL TO PLAY FOR, BARRY!
