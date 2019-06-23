---
layout: post
title: "Let's Build a Thing! (Part Two)"
date: 2019-06-23 11:03:00
comments: true
categories: js, creativity, front-end, hack, typescript, react, vue, web components
---

So yesterday I embarked on this mad, audacious goal of trying to create a front-end
framework from the ground-up using TDD and TypeScript. For those of you who'd like
to refresh your memory, you can find the original blog post [here](https://medium.com/@thesheps/lets-build-a-thing-part-one-7b29e4f8e955). But what does the world look like
today? Well. There was midnight oil burned and coffee drunk. There was trashy
background TV watched and lots of lovely downtime. My Trello board now looks liiiiike
this!

<center>
    <img src="{{site.url}}/assets/posts/2019-06-23/trello.png">
</center>

Now. You may notice in this screeny that I am my own User. I am Dev, QA, BA and PO.
I am literally wearing every single hat. And this is really tough. I am answerable to
nobody. No acceptance criteria are challenged. The overall design of _The System_ is
arrived at with no input from other developers. I am drawing on the experience of a
single person, and the testing of _The System_ is not validated by anyone other than
the person who originally developed it.

<center>
    <img height="400px" src="{{site.url}}/assets/posts/2019-06-23/hats.png">
</center>

And it's kinda freeing, in a way - but also pretty lonely. Pet projects such as these
really bring home how privileged I am to work with incredible people every day, and
how much fun I get to have working in tech! But enough of that fluffiness, what have I
actually _achieved??_

## The Observer Pattern

So this is _ancient,_ right? If you've not come across it [there's a super book you should take a look at!](https://www.amazon.com/exec/obidos/tg/detail/-/0201633612)
Simply put this pattern allows changes in the state of one component to **Notify** any
number of **Observers**. The way it does this is actually pretty low-tech. You
essentially have a **RegisterObserver** method on your Observed component which adds
new Observers to an array, and then you iterate over each of these Observers and notify
them whenever your state changes.

<center>
    <img height="400px" src="{{site.url}}/assets/posts/2019-06-23/observer-pattern.png">
</center>

Now, I've decided to use this pattern as a means to keep my DOM updated with any
changes that might happen in the structure of whatever _State-object-thing_ I've got
defined. Let's see if we can drive out that behaviour using tests!

### Test #1 - Observers are registered

<script src="https://gist.github.com/thesheps/33907b4174cdb4cd33bfb441730bbad1.js"></script>

In this test I've created a couple of in-test components to start investigating
observability. There's a dead simple `registerObserver` function that belongs to my
Controller class. Right now it should be adding the provided observer to a collection
so that we can subsequently used it to notify the world when something changes. But right now that's not happening. Let's fix that :fist:

<script src="https://gist.github.com/thesheps/4abe3bf920d2dc93f2b58c09872231ea.js"></script>

It's really that simple... Arguable this first test might be doing too much, but coming
in at only 20 lines of code (including assertions); I'm pretty happy! Shall we
refactor? I think we should. Right now we've got all the code in a single file. This is gonna get pretty hard to
organise and maintain as the app grows, so let's move things about a little bit?

<script src="https://gist.github.com/thesheps/7a57ec84b83eae63462f042f541f59e1.js"></script>

Splendid. Now we have a few separate .ts files which represent the functionality of a
couple of very well-defined components. I'm pretty happy with this folder structure for
now so let's move on, I guess? BUT WAIT! There's a problem with the design. Right now
we're exposing the Controller's `Array<Observable>` member as a public property. This
is kinda dangerous as it's no longer under the direct control of the Controller class.
Any class that has a reference to our Controller can update, reassign or delete the
Array's contents. Side effects are naughty! Let's make it private, but still provide
a mechanism to list its contents.

<script src="https://gist.github.com/thesheps/1c97269ce2333a2b35b00d534ca90c08.js"></script>

This is almost a trivial refactor again, but now the collection is defined as being private
and readonly. We also now have a `get` function which returns a mapped collection of the
original list of observers. Fab.

### Test #2 - Observers are notified

So now that we've found a way for Observers to be registered with our Controller, what about
being able to tell Observers about changes to state? Let's write a test to drive this out.
We'll start by adding a simple prop, then writing a test which asserts whether or not any
registered Observers are notified when it changes. The below gist is what I ended up with!

<script src="https://gist.github.com/thesheps/47738b7554a1abaea15b4b3f19839342.js"></script>

Here I'm using a simple TypeScript getter/setter combo to _hook_ into the setting of a simple
property, and using that as a springboard to iterate over all the Observers and update them
with the value that's changed. There are a few problems with this approach