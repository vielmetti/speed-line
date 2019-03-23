---
title: "Transform Graphs"
date: 2019-03-22T15:49:05-04:00
issue: 3
---

A build system transforms source code into executable
objects. It does this by constructing a graph of dependencies
among those elements, and then by executing code to
resolve those elements. Think of the files as the
nodes in the graph, and the command lines to compile
those files as the labels for the edges. This is a
directed acyclic graph (DAG), for compiles go in one
direction, and cycles are impossible to resolve.

With this graph abstraction, you can start to 
compare build systems by how they build the graph
of dependencies, and then how they execute operations
on that graph. In particular, I am interested in build
systems that operate with a very high degree of parallelism,
and that work effectively across large codebases.

If your code base is large, or if it changes rapidly,
it can be difficult to keep this graph up to date. This
at times creates so much pressure on the build system to 
keep up that it pays off to divert your attention from
the software you are working on to instead work on your
build system.

Some examples to illustrate.

The oldest build system still in common use is `make`.
`Make` uses the same program to compute dependencies
as it does to run the build process. The net effect of
this is to make it very expensive to run on large
builds where very little has changed. Make spends a
lot of time figuring out what to do, even when there's
not much to do. 

The next logical progression in build development is
`cmake`, which splits the problem of deciding what to
do with the task of actually doing it. Cmake generates
Makefiles, which can be then executed with make. But
Cmake can also generate files for other build systems,
and notably one of thoes is `ninja`.

`Ninja` is a fast build system out of Google. It was
motivated by Google's habit of building large, complex
pieces of software with lots of contributors and a rapid
pace of change. Projects like Google Chrome, which has
grown at a rapid pace, demand fast build tools just so
developers can keep up. `ninja` answers this call by being
extraordinarily simple and stripped down, more akin to
assembler than to a high level language.

Caching in this context is labeling the points or edges on the graph
in such a way that the system can say "we've done this before" and
skip forwards through the graph rather than repeating a previous
iteration. Caching can be really hard if you have fundamental 
dependencies (like your compiler, or a user interface library) that are 
in the process of simultaneous change. Well engineered caches can
dramatically speed up the evaluation of complex builds.

Big systems can have very big caches, and then you start to get 
into adjacent problem territory of network storage and cache validation.
If you are in Amsterdam and the cache for your compute is in Tokyo,
it can be appreciably faster to rebuild your results rather than
do a long fetch to a distant cache.

If you look at build systems through time, nothing has the staying
power of Unix's `make`. Dependencies are described in a `Makefile`,
and `make` faithfully builds them based on a top-down view of
what can happen next. Parallel `make` is capable of using multiple
cores on a system by recognizing independent paths through the
graph that it builds. 

It took me a bit to realize that `make` - which both draws the graph
and executes it - might not be the only way to divide up the problem
into parts. Make doesn't have visibility outside of the Makefile
that it lives in. It also doesn't really know what it's going to do
until it starts to do it. Big projects with disconnected Makefiles
often don't have a way for code to realize what can be done in parallel,
or to predict the downstream impact of a minor edit.

What if the thing that made the graph didn't have to execute it?
Then you'd have something like cmake + make, or cmake + ninja, where
the "graph building" and "graph executing" bits get separated.
The example of ninja is an interesting one, for its equivalent
of the makefile is so spare and stripped down that you nearly
have to use some other system to generate it. 

By making all of your dependencies and transformations explicit,
you can start to reason about the graph that results. "What
are all of the things that depend on foo.h?" is a question you'd
like to tackle directly and easily.

This only handles tasks within a single repository, though. 
As projects get bigger and absorb more big subsystems, you
start to want to reason about the whole codebase, and not want to
have external dependendcies that are untracked. This leads large
organizations towards the "monorepo", the one big pool of code
that includes everything and where a build can take into account
incremental changes across very deep dependencies.
