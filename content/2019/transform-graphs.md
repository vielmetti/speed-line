---
title: "Transform Graphs"
date: 2019-03-22T15:49:05-04:00
draft: true
---

A lot of things become easier for me to understand - but alas,
harder to draw - when expressed in terms of graphs and transformations
of graphs. 

For example, any build system that computes dependencies is drawing
a graph, and then resolving those dependencies (through compiles
or whatever) is effectively executing that graph.

Caching in this context is labeling the points or edges on the graph
in such a way that the system can say "we've done this before" and
skip forwards through the graph rather than repeating a previous
iteration.

It took me a bit to realize that `make` - which both draws the graph
and executes it - might not be the only way to divide up the problem
into parts.

What if the thing that made the graph didn't have to execute it?
Then you'd have something like cmake + make, or cmake + ninja, where
the "graph building" and "graph executing" bits get separated.

Once you have built a graph, you can start to reason about it. "What
are all of the things that depend on foo.h?" is a question you'd
like to tackle directly and easily.

ninja e.g. can do this for you: graph out the whole dependency tree.

