---
title: "Software Architecture and Design"
teaching: 15
exercises: 0
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

As a piece of software grows,
it will reach a point where there's too much code for us to keep in mind at once.
At this point, it becomes particularly important that the software be designed sensibly.
What should be the overall structure of our software,
how should all the pieces of functionality fit together,
and how should we work towards fulfilling this overall design throughout development?
 Similar to the software requirements, the actual implementation and timeline
of the development process should be documented. One example are the
[IEEE software design descriptions](https://ieeexplore.ieee.org/document/278258) and as 
indicated in the requirements episode, an adaption for the [Software taskforce of the Transients and Variable Stars
LSST Science Collaboration](https://lsst-tvssc.github.io/taskForces/software_task_force.html) 
can be found under `Documents`.


**Software design**, covers some of the following aspects:

- **Algorithm design** -
  what method are we going to use to solve the core business/science problem?
- **Software architecture** -
  what components will the software have and how will they cooperate?
- **System architecture** -
  what other things will this software have to interact with and how will it do this?
- **UI/UX** (User Interface / User Experience) -
  how will users interact with the software?

As usual, the sooner you adopt a practice in the lifecycle of your project,
the easier it will be.
So we should think about the design of our software from the very beginning,
ideally even before we start writing code -
but if you didn't, it's never too late to start.

The answers to these questions will provide us with some **design constraints**
which any software we write must satisfy.
For example, a design constraint when writing a mobile app would be
that it needs to work with a touch screen interface -
we might have some software that works really well from the command line,
but on a typical mobile phone there isn't a command line interface that people can access.


## Software Architecture

At the beginning of this episode we defined **software architecture**
as an answer to the question
"what components will the software have and how will they cooperate?".
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

## Addressing New Requirements

So, let's assume we now want to extend our application 
with some new functionalities (more statistical processing, a new view, etc.).
Let's recall the solution requirements we discussed in the previous episode:

- *Functional Requirements*:
  - SR1.1.1 (from UR1.1):
    reading light curves in different formats such as .csv, .json, .dat;
  - SR1.1.2 (from UR1.1):
    filtering out rows with NaN entries, where NaNs can be filled with different values (e.g. -99.9);
- *Non-functional Requirements*:
  - SR1.2.3 (from UR1.2):
    be able to determine periods for a hunder of light curves in under a minute.
    
### How Should We Test These Requirements?

Sometimes when we make changes to our code that we plan to test later,
we find the way we've implemented that change doesn't lend itself well to how it should be tested.
So what should we do? We could write unit tests. As we have seen before, it is therefore
a good idea to make sure that your software's features are modularised
and accessible via logical functions. 

We could also consider writing unit tests ensuring that the system meets
our performance requirement, so should we? In short, it's generally considered
bad practice to use unit tests for this purpose.
This is because unit tests test *if* a given aspect is behaving correctly,
whereas performance tests test *how efficiently* it does it.
Performance testing produces measurements of performance which require a different kind of analysis
(using _integration_ and _performance_ tests, as well as techniques such as [*code profiling*](https://towardsdatascience.com/how-to-assess-your-code-performance-in-python-346a17880c9f)),
and require careful and specific configurations of operating environments to ensure fair testing.
Furthermore, it is important to note that unit testing frameworks are not intended
for measuring system performance as a whole, as they only test individual units.
This limitation prevents stakeholders from gaining a comprehensive understanding of
the system's performance in real-world scenarios.

The key is to think about which kind of testing should be used
to check if the code satisfies a requirement,
but also what you can do to make that code amenable to that type of testing.

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
