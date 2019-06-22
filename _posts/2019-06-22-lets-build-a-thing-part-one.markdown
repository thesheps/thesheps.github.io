---
layout: post
title: "Let's Build a Thing! (Part One)"
date: 2019-06-22 09:27:00
comments: true
categories: js, creativity, front-end, hack, typescript, react, vue, web
---

My current run-rate for blogging is _not fantastic._ I have extremely good intentions,
but invariably I either:

- Stop doing "worky" things outside of the working week so that I can give my poor brain a rest.
  This I'm able to with reasonable success of late having learned how _not_ to cope with burnout!

- Get super caught-up in preparation for singing conventions/shows with [my barbershop chorus
  (shameless plug but I don't even care.)](https://cottontownchorus.co.uk)

- Get super caught up in learning a bit of tech to satisfy my own curiosity, or to help me out with
  future client engagements.

So I thought I'd try to use this as last reason as an opportunity to write something cool, and
also see about blogging its progress, too! So let's get down to it. Let's build...

## A FRONT-END FRAMEWORK

Because let's face it, [there aren't enough of them.](https://github.com/collections/front-end-javascript-frameworks).

Given that there are already thousands of fully-mature frameworks out there that allow you to build
beautiful, dynamic single page applications, and that they generally have great community support,
tooling and developer satisfaction; why build another one?

Put simply - because I wanna. I'm not satisfied with the _mysterious voodoo_ that happens behind
the scenes when you render a React app. I'm deeply interested in how the observability of a data
component might cause _only a partial_ re-rendering of a DOM. I'm keen as very keen thing to evolve
a performant web rendering framework from the ground up, and learn heaps about TypeScript, virtual
and shadow DOMs and Web Component architecture. But above all _I just want to_. I'm a software
engineer not because I'm chasing money or just as a rung on some career ladder - I'm a software
engineer because I love to build shit :)

So over the next X amount of days, weeks, months, years/til whenever I get bored, I'm going to build:

- A test-driven web component rendering framework.

- In TypeScript.

- Hosted on NPM.

## First things first

I am a great big Kanban-enthusiast, and like the majority of my engineer friends I love nothing
better than Listing the ever-loving shit out of my life. So first things first, let's go and do
a Trello thing:

<center>
    <img src="{{site.url}}/assets/posts/2019-06-22/trello-outline.png">
</center>

This is quite nice because it seriously helps to order my thoughts. Moving stories/tickets/stickies
across a board is comparable to the lovely little dopamine hit that you get from playing the slots
in Vegas, or from seeing someone :thumbsup: your post on Facebook. Agile is a gateway drug. Discuss!!

Anyways, I digress. What features should I build, then? Well for a start there are a few teeny tech
tasks to work on so that I can get my pen going:

<center>
    <img src="{{site.url}}/assets/posts/2019-06-22/trello-tasks.png">
</center>

First of all I want to configure Jest (my testing framework of choice!) to play nicely with
TypeScript. Next I'm going to want Webpack to transpile whatever stuff I'm coding into JavaScript.
I estimate this is going to take just a couple of hours of clicking around so let's start with that
right away!

## Configuring the development environment

Rather than spending aaaaages describing how I got to this state, I've created a gist which contains
each of the configuration files that I rolled to support TypeScript transpilation to native
Javascript, and the options for Jest to be able to understand TypeScript at runtime:

<script src="https://gist.github.com/thesheps/b90357c0a8fb6272d1620660b8966192.js"></script>

## .babelrc

This file configures what kind of transpilation you want to do. Here I'm configuring it to support
the `@babel/typescript` preset, with a bunch of other, sexy plugins which extend the base
functionality

## .jest.config.js

This file details some of the options that are needed to support files with the .ts extension.
Note, too that we're specifying the _root folder_ for Jest to find any test files to run.

## package.json

This snippet just lists the dev dependencies needed for NPM to be able to physically run my new
NPM module.

## tsconfig.json

The presence of a tsconfig.json file in a directory indicates that the directory is the root of a
TypeScript project. This is going to be called under-the-hood by webpack, and contains a few
compiler hints to improve our development experience (basically just a few imports to support
ES16/17).

## webpack.config.js

Finally this webpack configuration file outlines how our transpilation happens, and what the output
of the build is gonna be, where it's going to live etc.

With all the above in place, we can go ahead and write a super-simple failing test!!

<script src="https://gist.github.com/thesheps/25d9ad00079b114b85dc3809eefc5135.js"></script>

<center>
    Proof!!
    <img src="{{site.url}}/assets/posts/2019-06-22/test-output.png">
</center>

<hr>

Look, I'm really tired now. But this is all awesome - We've been able to build a simple walking
skelington that's all wired up properly and we can start writing some tests to build our our
functionality!

If you'd like to follow-along with this series of blog posts, there's a repo you can take a look at
[over here!](https://github.com/thesheps/toy-js)

Super.
