---
title: "Monorepo"
date: 2019-03-23T00:30:15-04:00
draft: true
---
A monorepo ("monolithic repository") is a single collection
of code that includes several related projects. Rather than
manage and build those projects separately, the monorepo
process looks to put the whole of the relevant codebase into
a single place.

Very large companies with a lot of code will use a monorepo
to hold it all. This lets them act across all dependencies
within their system at the same time, rather than chase 
bugs across separate components. Builds in a monorepo can
be faster because caching runs across the whole system.

When faced with code that has been extracted from a monorepo,
a word of caution is relevant. Big monorepos generate a lot
of pressure on build systems, and as a consequence new
build systems come into place that are complex and unfamiliar
to those who are used to the relative simplicty of `make`.
You may find yourself spending as much time tending to the
build system as to the code itself.

Inspired by the problems generally that people have with
Envoy, which is built with Bazel; Bazel is not a fun piece
of code to have to keep on top of. 

* https://blog.envoyproxy.io/external-c-dependency-management-in-bazel-dd37477422f5
