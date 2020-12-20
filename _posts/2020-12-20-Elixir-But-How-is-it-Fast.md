---
layout: post
title: "Elixir: But How is it Fast?" 
---

This post is a reflection on a conversation I had with a co-worker.  I had just switched jobs from coding Elixir full-time to doing low-level C++, and I was re-living a lot of the frustrations associated with going back to a language like C++.  I was describing the Elixir language and the Erlang runtime, picking the low-hanging fruit of discussion: immutable data, strong but dynamic typing, no concept of memory allocation or pointers.  We couldn't talk long, but that last point, no pointers, led him to ask me "But how is it fast?".

## Define Fast

There is a definition of fast that is an engineer favorite:  a timed run of some operation in one language compared to another.  I'm sure you've read them.  They are also typically iterative, as a run in a specific language is improved by some expert pointing out a rare or obscure characteristic or technique that slices off a few milliseconds or nanoseconds.

Let me tell you, the organizations that can spend weeks or months slicing time by implementing more and more obscure features into their production codebase are very few and far between.

Of course optimizing the crap out of your code can make it a bit faster...on a specific system...with a specific setup...on a specific use case.  But imagine what would happen if any of those things were changed?  If the C++ code was leveraging specific memory model details that are present on Intel processors, but outside forces end up deploying the code in production on AMD? or on an Intel chip but with architecture changes?

When dealing with non-academic systems that target production, there are multiple facets to fast; I would list some as:

1. Application runs "fast enough", this is the code speed everyone measures
1. Prototypes of new features are developed in a meaningful timeframe
1. Prototypes can be matured into a deployable state in a meaningful timeframe
1. The systems can be debugged and tested in a meaningful timeframe
1. The system can be re-deployed or migrated in a meaningful timeframe
1. Production problems can be detected, fixed, and patched in a meaningful timeframe

This is not an inclusive set, its just what I could ramble off the top of my head while writing this, but you can see that not only are most of these unrelated to the actual benchmark-speed of the application being written, but the rest re about how efficiently developers can work with the given application.  These aren't looking for hackathon numbers either, but time needed to move to a state that is production ready.

Lets list off some things that are on the wishlist of companies that have a production environment:

1. Application runs "fast enough"
1. Use cases have unit test coverage
1. Use cases have integration test coverage
1. Application is stable and error-free
1. Application is compatible with the deployment environment

Here again, only one item relates to the actual speed of the application, and like before, it is not "as fast as possible", but "fast enough".

## Fast Enough

When talking about systems that are designed and used in important environments, and even in very strict real-time environments, there is a concept of fast enough.  Nothing is specified to be as fast as possible.  For one reason, as fast as possible is an open-ended problem.  You do not want to have open-ended optimization problems standing between your team and a delivery deadline.  For a second reason, even in real-time systems, its not how fast your code runs that is important, just that its not too slow.  When writing for a real-time system you never guarantee what the best-case operating time is for your application; you specify the average case and the worst case.  You point and say "we will not be slower than X".

So if real systems have operating ranges and just need to be fast enough, why the focus on being as fast as possible?  With very few exceptions, as fast as possible is an academic exercise, and is not meaningful on real systems.  "But how can you say that?" I can imagine someone asking, and its related to perceptions of speed and bottlenecks.  Once the user of the system, whether human or not, can no longer tell how much faster you are, there is no benefit to being faster.  In a gaming computer monitor, features like VSync pop up to actually slow the GPU and synchronize it with the display.  Once going faster doesn't matter it turns into a resource management problem: do other work instead of going faster, enable more detail/resolution instead of going faster.

Any system with a ceiling on perception will be like this, there is a limit to usefulness for pixel density in a display, there is a limit to usefulness for a stereo system responsiveness.  Once you are in a position like this, your system just has to exceed X, it simply has to meet a metric, and going further is no longer necessary.

Systems Engineers and Architects may disagree with me, but any development that is not simply a low-level algorithm is actually a system.  Only a single function that is reentrant, has well defined input and well defined output, is not a system.  Once you have multiple functions, and are handling orchestration between them you are looking at more details:  Engineer cognitive load, code complexity, integration testing between the multiple units.  These take time and money in an organization, and for a lot of systems are more important than raw speed.

## Is Elixir Fast Enough

Wrapping back to the original question of how can Elixir be fast, is it fast enough?  Simply put, yes.  In a world where web APIs and database integration layers can be written in Python and NodeJS, where the slowest part of a transaction is probably the networking speed between hosts, the speed of an individual action is no longer important.  Fast Enough can be measured in milliseconds, and be within the margin of error of measuring network traffic.

Taking the web API as a talking point, what is fast enough now?  Nobody cares, as long as it is fast enough.  The pain point of even the fastest languages here, is that web APIs are not one-to-one.  See the [C10K problem](https://en.wikipedia.org/wiki/C10k_problem).  Its not having one action be fast enough, but being able to do ten thousand actions without any of them being too slow.  In most modern systems we are measuring and benchmarking the scalability of the system: How many transactions can we do per second while still being fast enough.

Don't misunderstand me, at my current employer Elixir would be a terrible choice, but we are:

1. Not doing anything in parallel, its a 1-few high throughput data pipeline system
1. Doing a lot of math and numerical calculations, which Elixir is not great at (yay trigonometric functions!)
1. Mandated to use AUTOSAR C++14, so its not really a question anyways.

But outside of those pipelines, where honestly NodeJS, Python and other languages would also get weeded out; Elixir is hands-down fast enough.  Feel free to dig into the elixir concurrency model yourself and see how it will run circles around the traditional concurrency models commonly used.  I also have a post about it [here](https://mjheilmann.github.io/2020/12/06/Concurrency-Models.html).  And coming in a post soon, Elixir is very fast to write and get to a stable production quality level!

## Gotta Go Fast

The next time an argument pops up about what is faster, please remember that almost nothing is in isolation. I am currently using C++ to get the fastest possible code in a low-level embedded system, but the amount of overhead, design, testing, redesign, and refactoring involved is absolutely immense.  I'll just point you to [Uncle Bob](https://en.wikipedia.org/wiki/Robert_C._Martin) on what is involved in doing something like that safely, correctly, and without issues in the code and in the organization using that code.