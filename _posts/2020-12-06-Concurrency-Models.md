---
layout: post
title: Concurrency Models
---

What is the concurrency model of the language or platform you are using?  How does it compare to the other models?  I'll wager that most engineers and coders just run with what they know, and if the need to scale happens they go with the flow.  However, knowing how a runtime handles concurrency, and how to scale it when you need to, is key when selecting what to implement your platform on, or what to migrate to next.

In this post I'll give a rundown of some concepts of concurrency, as well as discuss some models I am familiar with: C++, Python, NodeJS, GoLang, and Elixir.

## Tell me the difference between Parallelism and Concurrency

This is one of my favorite questions to pose in an interview.  I don't count it against the candidate if they cannot express the difference, as I haven't had one yet that can point to the difference between these two words.  Here are my internal definitions for these two terms:

> Parallelism: Making a process or algorithm so that it can run in parallel.  This relates to vertical scaling of a system (i.e. adding more cores)

> Concurrency: Making a program or algorithm able to handle more than one task at once.  This can mean working on other tasking while the primary task is waiting for input or external processes.

Both of these relate to getting more work done, and more efficiently using your resources.  They are not mutually exclusive, and can play very nicely together depending on your need and available resources.

Lets apply these definitions to some common languages, and see what the implications are.

## Concurrency Models

For the sake of discussion below, I will include a few models here in isolation, and then refer to them in the language sections below.

### Posix Threads

This is the way an operating system handles concurrency.  Every program or process has at least one thread of execution, and these threads are scheduled onto the processor cores that you have on your system.  Even when the thread is sleeping or doing nothing, all running threads will get a slice of a CPU, even if to check if they should wake up yet or not.

Windows and other platforms use win32 threads, or pthreads, or other names, but these operate similarly enough to posix threads for this post that I will simply call all of them Posix Threads.

Posix threads can use mechanisms for communication between them, and share memory if they are in the same process, or the memory is made available through hardware or OS mechanisms.

The problem with Posix threads, is that getting a time slice on the CPU for a thread involves fetching enough program to fill the CPU cache, doing some computation that the thread had pending, and then unloading the thread.  If you hav a system with significantly more threads than you have processor cores available, then more time in your system will be spent churning those threads and there is less time for the work to be completed.  Because of this, languages and runtimes that are dependent on the OS scheduler for their concurrency typically try to align their threads or workers with the number of CPU cores available.

### Event Loops

An event loop is a single thread of execution where work is scheduled onto it as part of the runtime.  Through design and planning it can have multiple data pipelines or function chains operating that are interleaved on the single Posix thread.  This allows a single core to be much more effectively shared among multiple tasks an application may be attempting to do.

Event loops are typically only a single thread, as it removes the thread-safety requirements of scheduling tasks onto multiple threads, and allows for in-order processing of the scheduled tasks.  This design characteristic has connotations when you need to do more than a single CPU-cycle of work.

### Micro-threading

Micro-threading is a runtime that uses a threading abstraction, and then schedules the threads onto available CPU cores itself.  There are several different ways to accomplish this, including using an interpretation layer for self pre-emptive behaviors.  These designs are still scheduling threads on the CPU cores, but are attempting to do so without invoking the Kernel-level CPU thread transition.  They typically have a scheduler or "runtime-thread" that executes the micro-threads, and the number of schedulers is matched to the cores in the system.

Because the micro-threads do not map to posix threads, and can number much much higher than the number of CPU cores before they start having resource contention, they are typically written in a synchronous, blocking fashion, and instead new micro-threads are spun up as workers, or to handle concurrency.

## Languages and their mechanisms

### C++

C++ is an older, and high performance language.  It compiles to native instructions and is not interpreted before it hits the CPU register.  It is used heavily in many of the key applications:  Nginx, Apache Web Server, Networking drivers, the NodeJS v8 interpreter, etc.  If you are working on embedded systems, or using a common framework like those listed, you are using the C++ concurrency model.

C++ is still being actively developed, and the recent versions from c++11 and later have started adding many language features that enable better concurrency.  These include Lambda support and Promises.

C++ uses the Posix threads model, and has direct access to low-level thread creation.  There are frameworks that can be used, like Qt, that use an event loop to shift processing from many threads to a few threads, but that is not part of the language design.

To get the most performance out of C++, you will be manually creating new threads, and then orchestrating their life-cycle and data-access needs while the program is running.

### Python

Python is a favorite from the data processing fields, but it is also powerful and capable for production use.  It is interpreted, and even when "pre-compiled" it is shifted to a byte-code that is fed through the python runtime.  It is much simpler to write than C++, with a performance tradeoff from the interpreter layer and garbage collection.  It has built-in hooks for splitting functions and work across threads, and across processes.  It is an interpreted language, so it has fast development iterations, and fantastic debugging support, but is not the fastest.

Like C++, Python is using the Posix threads model under the hood.  There are additional frameworks like Twisted that can run an event loop on top of the code to get better concurrency support.

Unlike C++, Python has a hidden tradeoff: The Global Interpreter Lock, or GIL.  The normal Python interpreter runtime is not its-self thread safe.  If you are starting threads in an older python version, you may find that only one is actually running at a time, and your concurrency can come in only when your python code is doing non-python things: running code through a c-lib, or waiting on networking calls.  Newer python versions alias threading to multiprocessing so mask this problem, but will invoke inter-process communications where you thought you were accessing a shared object in memory.

To get the most performance out of a Python server or service, you will find yourself running multiple python processes behind a load-balance of some kind, either explicitly or as a python framework feature.

### NodeJS

Both the C++ and Python sections discussed improving concurrency by running an event loop on top of the thread model.  NodeJS instead supports event loops directly as the only runtime option; NodeJS does not have or support threading primitives.  This is also an interpreted language, with its Chrome v8 interpreter from Google being written in C++.  We can technically call Javascript a c++ event-loop framework with its own scripting syntax, but that is neither here nor there.

This event-processing-first approach is simpler to reason around: all state can be shared because there is no consideration needed for race conditions between threads, nor are object protection or ownership practices needed.  No garbage collection, and limited value-vs-pointer interactions.  It is simple, straightforward, and operates very similarly to browser-based Javascript.

The tradeoff here is that without the ability to use threads or parallelism within the Javascript language, the recommended way to develop a NodeJS application is to actually develop many.  Split your application into Micro-services, that are run on different NodeJS instances, and have them communicate through external means, such as HTTP, message-queues, databases, or key-value stores.

When the throughput for a single micro-service exceeds the capability of that single NodeJS instance, you launch multiple NodeJS instances.  If these instances are not workers off of a database or message queue, but instead host HTTP endpoints, you will need a load-balancer or gateway in front of them to present them behind the single URL for access.

When writing NodeJS this way, each NodeJS instance should have its own CPU core on the host, and 1 GB of RAM reserved for its use, limiting the number of instances you are running to the cores available in your host machine, and/or your server cluster.

This is straight-forward and supported by many SAAS hosting solutions, but anecdotally I've had a service where I scaled to 200 multi-core Heroku instances and it was still struggling, but the story there is for another post.

### GoLang

GoLang is a purpose-driven language created at Google for doing Systems-level programming.  This is our first example that uses micro-threading, which GoLang refers to as "go-routines".  This is a play on words from "co-routines".

GoLang is a compiled language, meaning it does not have a direct runtime, is not interpreted, and is generally very fast.  It is typically used for systems-level coding and utilities, which includes the Docker engine.

GoLang can spawn thousands of micro-threads, and readily moves them between any and all CPU cores of your system.  Each of these micro-threads can block as long as they need to, potentially forever, as they are not stealing CPU cycles the way a posix thread can.

If you write an application in GoLang, and can implement your design in its object model, it will scale vertically as far as you and your design is able, and is able to utilize all of the resources on a single host system.

### Elixir

Elixir is unusual compared to the other languages here.  It is interpreted, compiling to, and running on, the Erlang OTP runtime.  It is functional, without any hooks from Object-Oriented programming.  It also uses the micro-threading model, which is also shared with Erlang.

Elixir is compiled to the common byte-code for the Erlang runtime (similar to how Java and .Net compile to byte-code), but it is full in on its implementation design.  Due to its design it often ends up with micro-services as well, but these are deployed in-process on micro-threads, instead of being separate instances.  It is an interesting exercise in separation of concerns.

Like GoLang, Elixir can spawn thousands of micro-threads, here called "processes", which will also pre-empt each-other.  Fun fact, I had an Elixir application where I had a case that caused infinite recursion; and I couldn't tell until I came back to unit testing, as it did not crash the system, or steal enough resources to be noticeable.  I believe the GenServer module uses infinite recursion to hold state.

If you write an application in Elixir, and can implement your design in its object-less, functional model, it will scale vertically and is able to use all the resources available on your host system.

Unlike GoLang, Elixir (and Erlang), have runtime features that enable clustering, so scaling horizontally is possible too once you are familiar enough with the features.  That is a topic for another post though.

## My Favorites

Obviously I will have opinions and have my favorite languages.  Which language I use for projects depends on the needs of the project, and having a large toolbox of languages can make starting out on the right foot so much easier.

Elixir is my favorite language for HTTP web-services.  Its micro-threading is perfect for supporting a high number of concurrent connections, including persistent connections like web-sockets.  Its data model treats data as immutable, and it has excellent testing support.  What it is lacking is numerical performance.  I think this is the bees knees until you need it to do some math, but math operations are CPU-specific, so the micro-threads will queue there.

Python is my favorite scripting and toy language.  Using it to orchestrate other processes, or leaving it running in the corner to slog through some batch-processing leverages the languages ease of use and processing hooks, without asking it to scale in a time-sensitive manner.

I am currently writing C++ full-time for work.  I enjoy the direct application of design patterns, interface segmentation, and having code that runs fast; but I don't think I would select this on purpose without clear performance benefits.

Javascript is OK, and I use it all the time for web programming.  It doesn't have the speed or error handling I want to see for server-side languages.  I have also not used Javascript directly, opting for es6 with a conversion step, or Typescript.

## Wrap Up

None of these are bad, and there are more languages with their own twists on these models, or perhaps models I haven't met yet.  But you should be aware of them so that you can select the best for your needs when you need a scalable system.