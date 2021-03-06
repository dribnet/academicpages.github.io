---
layout: single
title:  "Perception is all you need"
header:
  teaser: "/images/500x300.png"
categories: 
  - Art
tags:
  - neural networks
  - perception
date: 2018-03-31
permalink: /posts/2018/03/perception-engines/
---
A visual overview 
of an exploration probing of the ability of neural networks to create
abstract representation from collections of real world objects.
A technique called _perception engines_ in introduced that 
is able to construct physical objects and powered primarily by
computational perception.
This system was used to
create "Treachery of Imagenet", a series of 12 ink prints based
on ImageNet categories, and permutations of this system are the basis
of ongoing and future work.

**Post Status: First Complete Draft - Nearly Done?**

![Treachery of ImageNet: forklift, ruler, sewing machine](https://user-images.githubusercontent.com/945979/37252510-d35ac436-2586-11e8-85e8-f5247fa78a2a.jpg)
<p align="center">Three prints from the recent Treachery of ImagNet series</p>

(suggestions? please [suggest an edit](https://github.com/dribnet/dribnet.github.io/edit/master/_posts/2018-03-11-perception-engines.md) as a merge request!)

Introduction
======
The core question I set out to explore was: can neural networks
create abstract objects from nothing other than collections
of examples segregated into an ontology of human concepts? 
Neural networks excel at perception and categorization, could
it possibly be that with the right feedback loops perception
is all you need to drive a constructive creative process?
The construction processes I am interested in are drawing
systems that result in real world objects.

Ultimately I was able to build a system that is able to express
abstract concepts within the constraints of a given drawing system.
Neural Networks excel at classfing images: given an image
they can assign it to a catgory such as fan, baseball, or ski mask.
In this project, the system generates abstract representational prints
that are intended to elicit these same responses in neural networks.
This process developed is called *perception engines* as it uses the
perception ability of trained neural networks to guide
its construction process. When successful, the technique is found to generalize broadly
across neural network architectures. It is also interesting to consider
when these outputs do (or don't) appear meaningful to humans.  
This post examines one result and deconstructs the process
behind it.

The perception abilities of a classifier are grounded in hundreds of example images
of a particular concept - in this case images from the category "electric fan".
So the only source of ground truth for any drawing is an unfiltered
collection of images from ImageNet. Here are the first few dozen
training images from the electric fan category:

![ImageNet: First 49 examples of Electric Fan](https://user-images.githubusercontent.com/945979/37649300-3442c382-2c96-11e8-8736-eec7b73f05cb.gif)
<p align="center">Random samples from the "electric fan" category</p>

Ultimately this will be transformed (with no human intervention) 
into an abstract visual representation
of "electric fan" - this two color ink print:

![Abstract representation of fan: two color ink print](https://user-images.githubusercontent.com/945979/37562897-12e5bd9e-2ad8-11e8-8c59-38cc8e3f4387.png)
<p align="center">Abstract ink print for the category "electric fan"</p>

This essay is a guide through the steps in building up the system able to achieve this.

First Systems
======
![Early images: birdhouse, traffic light, school bus](https://user-images.githubusercontent.com/945979/37252509-cfcf4ed6-2586-11e8-9730-519f37f5b855.png)
<p align="center">Early drawing systems used lines and rectangles with loosely constrained colors.</p>

The first perception engines were not concerned with physical embodiment.
They were pixel based systems which were inspired by and re-purposed the techniques
of Adversarial Examples. Adversarial Examples are a body of research which probes
neural networks with small perturbations to the input image in order to cause
the neural network to misclassify its output.

Adversarial Examples are usually constrained to making small changes to existing images. However, 
this work generally allows arbitrary changes within the constraints of a drawing system. Adversarial techniques also usually target specific neural networks. The goal here was to create images that generalize across all neural networks and - hopefully - humans as well. So perception engines use ensembles of trained networks with different well known architectures and also test for generalization.

Architecture
======
![Architecture diagram](https://user-images.githubusercontent.com/945979/37874286-0849e480-3089-11e8-91df-5616cf2cb914.png)
<p align="center">Perception Engines Architecture</p>

As the architecture of these early systems settled, the operation could be cleanly divided into
three different submodules:

  * Drawing system - The system of constraints involved in creating the image. The early systems used lines or rectangles on a virtual canvas, but later sytems achived the same result for marks on a page under various production tolerances and lighting conditions. 

  * Creative objective - What is the expressive goal? Thus far the
  focus has been on using neural networks pre-trained on ImageNet with an
  objective of maximizing response to a single ImageNet class. This
  is also consistent with most Adversarial Example literature.

  * Planning system - How is the objective maximized? Currently random search
  is used, which is a type of blackbox optimization
  (meaning no gradient information is used). Though not particularly efficient,
  it is otherwise a simple technique and works well in practice
  over hundreds to thousands of iterations. It also finds a "local maximum",
  which means in practice means it will converge on a different solution each run.

Growing a fan
======
The perception engine architecture defines uses the random search of
the plannig module to gradually achieve the objective through iterative refinement.
When the objective is to maximize the preception of an electric fan, the system
will incrementally draw or refine a proposed design
for a fan.  Combining these systems feels like using
a computational ouija board: several neural networks simultaneously
nudge and push a drawing toward the objective.

![First 78 steps in creating the Electric Fan drawing](https://user-images.githubusercontent.com/945979/37649310-3a66f544-2c96-11e8-83db-b4ddc844bd32.gif)
<p align="center">Early steps in creating the Electric Fan drawing.</p>

Though this is effective when optimizing for digital outputs, additional
work is necessary when planning physical objects which are subject to
production tolerances and a range of viewing conditions.

Modeling physical artifacts
======
![Left: Loading Purple Ink drum into Riso printer / Right: "Electric Fan" print before adding second black layer](https://user-images.githubusercontent.com/945979/37252784-67e24ac0-258c-11e8-8cab-565717e2284b.png)
<p align="center">Left: Loading Purple Ink drum into Riso printer<br>
Right: "Electric Fan" print before adding second black layer.</p>

After the proof of concept I was ready to target a physical drawing system. The Riso Printer was chosen as a first target; it
employs a physical ink process similar to screen printing. This meant all outputs are subject to a number of production constraints such as limited number of
ink colors (I used about 6) and unpredictable layer alignment between layers of
different colors.

At this point in my development I was awarded a grant from Google's [Artist and Machine Intelligence group](https://ami.withgoogle.com/) (AMI).
With their support, I was able to print a series of test prints and iteratively improve
my software system to model the physical printing process. Each source of uncertainty that could cause a physical object to have variations in appearance is modeled as a distribution
of possible outcomes.

Issue #1: Layer Alignment
-----------
It is common for Riso prints to have a small amount of mis-alignment between layers because the paper must
be inserted separately for each different color. This possibility was handled by applying a small amount of jitter
manually between colors.

![Electric fan with jitter](https://user-images.githubusercontent.com/945979/37575710-7b18fe0c-2b8d-11e8-8db2-a1490a831dfd.gif)
<p align="center">Examples of jitter being applied to produce a distribution of possible alignment outcomes.</p>

In practice this jitter keeps the final design from being overly dependent on the relative
placement of elements across different layers.

Issue #2: Lighting
-----------
The colors of a digital image can be given exactly. But a physical object will be perceived with slightly different
colors depending on the ambient lighting conditions. To allow the final print to be effective in a variety of
environments, the paper and ink colors were photographed under multiple conditions and then simulated as
various possibilities. 

![Electric fan with different lighting conditions](https://user-images.githubusercontent.com/945979/37575709-7aefb862-2b8d-11e8-88e9-9e78e24b6b96.gif)
<p align="center">Examples of lighting variations being applied to produce a distribution of possible alignment outcomes.</p>

The lighting and layer adjustments were independent and could be applied concurrently.

![Electric fan: jitter and lighting](https://user-images.githubusercontent.com/945979/37575708-7ac59c62-2b8d-11e8-9b9c-a65024631b26.gif)
<p align="center">Examples of jitter and lighting variations being applied to produce a larger distribution of possible outcomes.</p>

Issue #3: Perspective
-----------
In a physical setting, the exact location of the viewer is not known. To keep the print from being dependent
on a particular viewing angle, a set of perspective transformations were also applied. These are generally
added during a final refinement stage and are done in addition to the alignment and lighting adjustments.

![Electric fan with perspective transformations](https://user-images.githubusercontent.com/945979/37575707-7a9a26ae-2b8d-11e8-873c-4ace927313f0.gif)
<p align="center">Examples of perspective transform being added to produce a distribution of possible viewing angles.</p>


Final Print
========
This system typically runs for many hours on a deep learning workstation in order to generate hundreds to thousands of iterations on a single design.
Once the system has produced a candidate, a set of master pages are made. Importantly, the perspective and jitter
transforms are disabled to produce these masters in their canonical form. For the fan print, two layers
were produced: one for the purple ink and one for black.

![Separated layers](https://user-images.githubusercontent.com/945979/37576303-afdd6544-2b90-11e8-9748-5ef7e8d71747.png)
<p align="center">Final aligned layers of Electric Fan as they are sent to be printed.</p>

These masters are used to print ink versions on paper.

![Example print](https://user-images.githubusercontent.com/945979/37562897-12e5bd9e-2ad8-11e8-8c59-38cc8e3f4387.png)
<p align="center">Ink print from the master above (no two are exactly alike).</p>

Evaluating
----------
After printing, we
can use a photo to test for generalization. We do this by querying neural networks that were not
involved in the original pipeline to see if they agree the objective has been met - an analogue
of a train / test split across trained networks with a different architectures. In this case, the electric fan image was produced with the influence of 4 trained networks, but generalizes well to 5 others.

![Network responses to Electric Fan print](https://user-images.githubusercontent.com/945979/37253079-931239d0-2591-11e8-9533-a1904c900fce.png)
<p align="center">This electric fan design was "trained" with input from inceptionv3, resnet50, vgg16 and vgg19 - and after printing scores well when evaluated on all four of those networks (circled in red). However, this result also generalizes well to other networks as seen by the strong top-1 scores on four other networks tested (and a lower top-3 score on nasnetmobile).</p>


Constraint System as Creativity
=====================
A philisophical note on creativity and intent: Using perception engines inverts the stereotypical creative relationship employed in human computer interaction. Instead of using the computer as a tool, the Drawing System module can be thought of a special tool that the neural network itself drives to make its own creative outputs. As the human artist, one of my own main creative inputs is the design of a programming design system that allows the neural network to express itself effectively and with a distinct style. I've designed the constraint system that defines the form, but the neural networks are the ultimate arbiter of the content.


Treachery of ImageNet
=====================
![Treachery of ImageNet: complete series](https://user-images.githubusercontent.com/945979/37252592-3c064fa4-2588-11e8-947a-0f7c89fe50fa.jpg)
<p align="center">All 12 prints in the Treachery of ImageNet series</p>

In my initial set of perception engine objects
I decided to explicitly caption each
image with the intended target concept. Riffing off of Magritte's 
[The Treachery of Images](https://en.wikipedia.org/wiki/The_Treachery_of_Images) (and
not being able to pass on a pun), these first prints were called *The
Treachery of ImageNet*. The conceit was that many of these prints would 
strongly evoke their target concepts in neural networks in the same way people
may find Magritte's painting evocative of an actual, non-representational pipe.

Ongoing work
============
Other series are currently in various stages of production using the same core architecture. These use the same objective and planner but vary the drawing system, such as using multiple ink layers or using more generic screen printing techniques. Further out, other series are being prototyped which using different objectives or more radical departures from the current types of drawing systems and embodiments. As these are completed I'll share incremental results [on twitter](https://twitter.com/dribnet) with occasional writeups here, and I also maintain an [online store](http://dribnet.bigcartel.com/) that sells completed prints and funds future work.
