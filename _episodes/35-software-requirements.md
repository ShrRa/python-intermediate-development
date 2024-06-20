---
title: "Software Requirements"
teaching: 15
exercises: 5
questions:
- "Where do we start when beginning a new software project?"
- "How can we capture and organise what is required for software to function as intended?"
objectives:
- "Describe the different types of software requirements."
- "Explain the difference between functional and non-functional requirements."
- "Describe some of the different kinds of software and explain how the environment in which software is used constrains its design."
keypoints:
- "When writing software used for research, requirements will almost *always* change."
- "Consider non-functional requirements (*how* the software will behave) as well as functional requirements (*what* the software is supposed to do)."
- "The environment in which users run our software has an effect on many design choices we might make."
- "Consider the expected longevity of any code before you write it."
---

The requirements of our software are the basis on which the whole project rests -
if we get the requirements wrong, we'll build the wrong software.
However, it's unlikely that we'll be able to determine all of the requirements upfront.
Especially when working in a research context,
requirements are flexible and change as we develop our software. While there are 
guidelines for preparing and carrying out a requirements analysis (e.g. a
comprehensive overview can be found 
in the [IEEE Guide for Software Requirements Specifications](https://ieeexplore.ieee.org/document/278253)),
to an unprepared software developer they may seem overly formalized and impractical. 
However, in the essense, requirements are simply a set of wishes for the software,
described in an univocal and well-defined terms. Writing requirements it least in some form
can save you weeks of time.  
The [Software taskforce of the Transients and Variable Stars
LSST Science Collaboration](https://lsst-tvssc.github.io/taskForces/software_task_force.html) 
maintains documents related to various software development aspects including templates
for software requirements and design documents for the astronomical software.

## Types of Requirements

Requirements can be categorised in many ways,
but at a high level a useful way to split them is into
*business requirements*,
*user requirements*,
and *solution requirements*.
While this terminology comes from business, we can adapt
it to the academia reality. Let's take a look at these now.

### Business Requirements

Business requirements describe what is needed from the perspective of the organisation,
and define the strategic path of the project,
e.g. to embark on a new research area or collaborative partnership.
These are captured in a Business Requirements Specification.

For adapting our research light curve project, example business requirements could include:

- BR1: Functionality for analyzing periodic light curves, such as observations of RR Lyrae that we already have;
- BR2: Functionality for analyzing transient light curves, such as observations of Supernovae Ia;

### User (or Stakeholder) Requirements

These define what particular stakeholder groups each expect from an eventual solution,
essentially acting as a bridge between the higher-level business requirements
and specific solution requirements.
These are typically captured in a User Requirements Specification.

For our light curve project,
they could include:

- UR1.1 (from BR1): a possibility to process light curves from different surveys;
- UR1.2 (from BR1): support of a period determination algorithm;

For the second business requirements, the user requirements can be the following:

- UR2.1 (from BR2): support of a transient event detection algorithm;
- UR2.2 (from BR2): a possibility to automatically classify SNe types.
  
### Solution Requirements

Solution (or product) requirements describe characteristics that software has to have in order to
satisfy the stakeholder requirements. They fall into two key categories:

- *Functional requirements* focus on functionalities and features of a solution.
  For our software, building on our user requirements, e.g.:
    - SR1.1.1 (from UR1.1):
      reading light curves in different formats such as .csv, .json, .dat;
    - SR1.1.2 (from UR1.1):
      filtering out rows with NaN entries, where NaNs can be filled with different values (e.g. -99.9);
- *Non-functional requirements* focus on
  *how* the behaviour of a solution is expressed or constrained,
  e.g. performance, security, usability, or portability.
  These are also known as *quality of service* requirements.
  For our project, e.g.:
    - SR1.2.3 (from UR1.2):
      be able to determine periods for a hundred of light curves in under a minute.

This way, the business, user and solution requirements correspond to the different levels of 
implementation. Knowing the business requirements allows you to design the software in a more efficient way,
e.g. by keeping in mind that the functionality for loading and pre-processing the light curves has to support not only periodic,
but also transient light curves. At the same time, solution requirements often determine which tools you are
going to use (e.g. whether you should take parallelization into account).

#### The Importance of Non-functional Requirements

When considering software requirements,
it's *very* tempting to just think about the features users need.
However, many design choices in a software project quite rightly depend on
the users themselves and the environment in which the software is expected to run,
and these aspects should be considered as part of the software's non-functional requirements.


> ## Optional Exercise: Requirements for Your Software Project
>
> Think back to a piece of code or software (either small or large) you've written,
> or which you have experience using.
> First, try to formulate one key business requirement,
> then derive these into one user and then one solution requirement
> (in a similar fashion to the ones above in *Types of Requirements*).
{: .challenge}


### Long- or Short-Lived Code?

Along with requirements, here's something to consider early on.
You, perhaps with others, may be developing open-source software
with the intent that it will live on after your project completes.
It could be important to you that your software is adopted and used by other projects
as this may help you get future funding.
It can make your software more attractive to potential users
if they have the confidence that they can fix bugs that arise or add new features they need,
if they can be assured that the evolution of the software is not dependant upon
the lifetime of your project.
The intended longevity and post-project role of software should be reflected in its requirements -
particularly within its non-functional requirements -
so be sure to consider these aspects.

On the other hand, you may want to quickly create some code
to demonstrate a concept or perform a brief calculation before discarding it.
But can you be sure you'll never want to use it again?
Maybe a few months from now you'll realise you need it after all,
or you'll have a colleague say "I wish I had a..."
and realise you've already made one.
A little effort now could save you a lot in the future.

## From Requirements to Implementation, via Design

In practice, these different types of requirements are sometimes confused and conflated
when different classes of stakeholder are discussing them, which is understandable:
each group of stakeholders has a different view of *what is required* from a project.
The key is to understand the stakeholder's perspective as to
how their requirements should be classified and interpreted,
and for that to be made explicit.
A related misconception is that each of these types are simply
requirements specified at different levels of detail.
At each level, not only are the perspectives different,
but so are the nature of the objectives and the language used to describe them,
since they each reflect the perspective and language of their stakeholder group.

It's often tempting to go right ahead and implement requirements within existing software,
but this neglects a crucial step:
do these new requirements fit within our existing design,
or does our design need to be revisited?
It may not need any changes at all,
but if it doesn't fit logically our design will need a bigger rethink
so the new requirement can be implemented in a sensible way.
We'll look at this a bit later in this section,
but simply adding new code without considering
how the design and implementation need to change at a high level
can make our software increasingly messy and difficult to change in the future.


{% include links.md %}
