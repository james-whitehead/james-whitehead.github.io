---
published: false
title: News of the World - Part One
layout: post
excerpt: Developing a map of the news
comments: true
---
News of the World is a (very) small JavaScript project about showing local news headlines on a world map. All the ideas, pitfalls and workarounds found during development will be documented here in a semi-blog/semi-tutorial. This is part one.

## Reinventing the Wheel

One of the more common adages is "don't reinvent the wheel". The idea that part of the software you're trying to build has already been built by someone else and built better, so you should just use that. There are reasons to _not_ invent the wheel, of course, but it's quicker, easier and safer to use pre-existing libraries and frameworks.

Going down this route, however, has its own problem. Which wheels do you actually use? The JavaScript ecosystem is especially enormous, and it's easy to get paralysed with indecision over what to use. Do you use vanilla JavaScript, or do you use a framework? If you're using a framework, do you use Node, Angular, React, Vue, or something else entirely? If you're using a framework, do you need a package manager, a build system and a transpiler? If you do, which ones do you choose? If you're on the peripheries of web development, like I am, some of these decisions can seem impossible. [_How it feels to learn JavaScript in 2016_](https://hackernoon.com/how-it-feels-to-learn-javascript-in-2016-d3a717dd577f) is a slightly hyperbolic and very relatable view of how trying to get started in this feels. In the end, I settled on using pure JavaScript, because many of the frameworks seem to revolve around making single-page apps. While News of the World only uses a single page, this is because it's simple enough to do so anyway.
