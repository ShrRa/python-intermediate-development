---
title: "Software Architecture and Design"
teaching: 15
exercises: 20
questions:
- "What should we consider when designing software?"
- "How can we make sure the components of our software are reusable?"
objectives:
- "Understand the use of common design patterns to improve the extensibility, reusability and overall quality of software."
- "Understand the components of multi-layer software architectures."
keypoints:
- "Planning software projects in advance can save a lot of effort and reduce 'technical debt' later - even a partial plan is better than no plan at all."
- "By making our software modular, i.e. introducing parts with a single responsibility, we avoid having to rewrite it all when requirements change.
Such components can be as small as a single function, or be a software package in their own right."
- "When writing software used for research, requirements will almost *always* change."
- "*'Good code is written so that is readable, understandable, covered by automated tests, not over complicated and does well what is intended to do.'*"
---

## Introduction

In this episode, we'll be looking at how we can design our software
to ensure it meets the requirements,
but also retains the other qualities of good software.
Software design, as opposed to software requirements, deals with **how** a project will be realized in
terms of data structures, algorithms and system architecture. Requirements, on the other hand,
specify **what** must be accomplished.

Software engineering borrowed this term, and a few other terms,
from architects (of buildings) as many of the processes and techniques have some similarities.
One of the other important terms we borrowed is 'pattern',
such as in **design patterns** and **architecture patterns**.
This term is often attributed to the book
['A Pattern Language' by Christopher Alexander *et al.*](https://en.wikipedia.org/wiki/A_Pattern_Language)
published in 1977
and refers to a template solution to a problem commonly encountered when building a system.

Design patterns are relatively small-scale templates
which we can use to solve problems which affect a small part of our software.
One example is a strategy pattern that could handle multiple algorithms and handling them
in a consistent way. Architecture patterns are similar,
but larger scale templates which operate at the level of whole programs,
or collections or programs. Model-View-Controller 
is one of the best known architecture patterns. During the development process, 
programmers using the Python web framework Django will encounter it frequently.

Many patterns rely on concepts from Object Oriented Programming and 
there are many online sources of information about design and architecture patterns,
often giving concrete examples of cases where they may be useful.
One particularly good source is [Refactoring Guru](https://refactoring.guru/design-patterns).

## Best Practices for 'Good' Software Design

Aspirationally, what makes good code can be summarised in the following quote from the
[Intent HG blog](https://intenthq.com/blog/it-audience/what-is-good-code-a-scientific-definition/):

> *“Good code is written so that is readable, understandable,
> covered by automated tests, not overcomplicated
> and does well what is intended to do.”*

By taking time to design our software to be easily modifiable and extensible,
we can save ourselves a lot of time later when requirements change.
The sooner we do this the better -
ideally we should have at least a rough design sketched out for our software
before we write a single line of code.
This design should be based around the structure of the problem we're trying to solve:
what are the concepts we need to represent
and what are the relationships between them.
And importantly, who will be using our software and how will they interact with it?

Importantly, there is only so much time available.
How much effort should we spend on designing our code properly
and using good development practices?
The following [XKCD comic](https://xkcd.com/844/) summarises this tension:

![Writing good code comic](../fig/xkcd-good-code-comic.png){: .image-with-shadow width="400px" }

At an intermediate level there are a wealth of practices that *could* be used,
and applying suitable design and coding practices is what separates
an *intermediate developer* from someone who has just started coding.
The key for an intermediate developer is to balance these concerns
for each software project appropriately,
and employ design and development practices *enough* so that progress can be made.
It's very easy to under-design software,
but remember it's also possible to over-design software too.

{% include links.md %}
