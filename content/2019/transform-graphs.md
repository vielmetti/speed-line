---
title: "Transform Graphs"
date: 2019-03-22T15:49:05-04:00
issue: 3
---

A lot of things become easier for me to understand - but alas,
harder to draw - when expressed in terms of graphs and transformations
of graphs. I realize at times that this makes me come to conclusions
that look like leaps of logic, especially when it means moving
from one problem domain to another. "Wait, how did you get there?"
Most of the time that leap of logic is a shift in perspective
to see a new aspect of relationships. 

One place that I'm seeing graphs everywhere is in systems that
build software. This is most evident when there's a bug that you're
chasing that turns out to reflect a dependency on some other system
or subsystem. One moment you're working on machine learning, and
the very next moment you're contemplating language standards. When
the connection is too tenuous, you wonder if you're just going down
rabbit holes! At scale, though, those rabbit holes start to
interconnect, and you have a whole deep set of interrelated issues
to tackle.

For example, any build system that computes dependencies is drawing
a graph, and then resolving those dependencies (through compiles
or whatever) is effectively executing that graph. The more complex
your dependencies, and the more frequently they are changing (either
from internal or external pressure) the more effort you have to 
put into keeping this graph tested and up to date. When faced with
this pressure, sometimes the only way to resolve it is to reconfigure
the build process - leaving your original task to languish for
a while until you sort out the adjacent problem.

Graphs are made up of points connected by edges. In particular,
in this context they are usually directed graphs that go from 
input to output but not in reverse, and acyclic graphs that don't
have loops. These directed acyclic graphs (DAGs) directly model
the process of taking source code and headers and libraries and 
transforming them into usable software.

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
