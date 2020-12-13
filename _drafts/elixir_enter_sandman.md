---
layout: post
title: "Elixir: Enter Sandman" 
---

This post discusses what my first steps into Elixir were, and how I approached it from the perspective of a Node/Mongo Full-stack developer.

## Setting the scene

I was a NodeJS/ReactJS full-stack engineer at the time.  I had been hired as a heavy-hitter to speed up development and potentially fix some issues with the existing NodeJS stack.  This stack is strongly coupled to Sails.JS, and we were having significant problems.  Our performance was low, with high latency.  We had been compensating for this by simply scaling our SAAS hosting service higher and higher, eventually entering into negotiations with our provider to have our instance limit raised.

I don't know about you, but having to run 200 instances, each with multiple NodeJS processes, to handle the level of traffic and spiky-ness we were seeing was giving me bad feelings.  I had to start some open-ended investigations into our current stack to better understand why we were having trouble.  The website and media surrounding SailsJS suggested it was "enterprise ready" (an unprovable characteristic).

After seeing some articles, bugs, and critics of SailsJS that gave me concerns for our current use-case; I resorted to a sanity check:  I threw together a barebones ExpressJS micro-service that replicated one of our endpoints to do some basic local benchmarking.  Who knows, we could have been hamstrung by our SAAS hosting provider.

As of this writing, this was years ago, and I didn't keep the initial metrics, but here is the basic layout:

- We were using a custom auth token that resembled cookies
- We were using advanced Sails.js query language features, which involved creating a query object, encoding it as a JSON string, and then url-encoding that and embedding it in the request URL
- The endpoint receives this query string, parses and transforms it into a MongoDB query
- The endpoint does a caching-check against a Redis service on a different SAAS host
- If the cache fails, it queries Mongo for the results.  If the cache succeeds, it retrieves the cached DB call from Redis
- The Endpoint then uses the auth token to scope the session, and transform the DB call result into an appropriate JSON document for return to the calling client

Discussions about using custom auth, and how this was currently being cached aside, this was the behavior of our Sails system, so that is what my express clone did as well.  My initial results were that ExpressJS, doing the same tasking, was significantly faster.

It was time to schedule a meeting with the other engineers, I was going to propose migrating off of SailsJS, essentially rewriting the entire API.

Note here, we had a size-able user-base already, so like it or hate it, the query language that the mobile apps and our web portals were using was here to stay.  Also note, we were using Amazon's API Gateway as a front to our SAAS hosted API.  We used it for our domain, and some caching where we didn't have to customize the response per user.

## A simple Meeting

The meeting not only went a lot smoother than I had expected -- turns out the other Engineers and the CTO had their suspicions that I had only confirmed.  I did surprise them with one detail though: I wanted to do a hard rewrite.  I had concerns that a soft, or in-place piece-meal rewrite would enable the copy-paste of some bad code and bad habits from the existing API.

I got interesting feedback from our Architect, who is typically focused on our mobile app.  He said that if we are looking at a rewrite anyways, check out Elixir.  We had discussed various platforms before, but I learned that he, and another engineer, had attended the Elixir conference the previous year.

I agreed to scrape together another clone using Elixir and the Phoenix Framework.

## Enter Elixir

I wasn't familiar with Elixir, and had to go educate myself.  I had done programming that was in a functional-style; I prefer python functions over classes, and I had a few years using vanilla c99 ANSI-C, but this was an honest functional paradigm.

Elixir has the following traits that I'll list here for brevity:

- Elixir is dynamically typed, nothing new here coming from NodeJS
- Elixir is not an imperative language, I might need to do a post on this
- Elixir is a partially interpreted language, it compiles to byte-code and executes on the Erlang runtime
- Elixir was a pretty young language, but Erlang, and its runtime, were decades old and designed for old-school telecoms
- Elixir concurrency is using micro-threads, called processes, and supports thousands of these to exist; use these instead of threads or promise-chains
- Elixir data types are immutable, meaning that A - Elixir doesn't have nor need mutexes, and B - data structures cannot be updated, only new copies made that are slightly (or greatly) different
- My goodness, pattern matching!

I wasn't a good Elixir dev at the time.  I didn't keep the prototype, I didn't know what I was doing when I wrote it.  I wrote too much code, I checked unnecessary things, I wasn't leveraging the hooks in Phoenix for auth, routing, etc.  But it was interesting to try it out.

## My findings, round 2

The initial findings were a massacre.  But thanks to some help from the engineers who knew more about elixir at the time, I learned to restrict the number of Erlang schedulers to 1, it had been running on every core of my workstation and unfairly scaling above NodeJS.

With that fixed, the initial findings were a massacre.  My junior-level elixir code, while still using the same Redis and Mongo SAAS services, were significantly faster.  I reran the tests with local Redis and mongo docker containers to cut out networking variability, and it was still much faster.  A single response was faster, and clustered responses in a load-bearing benchmark, even with local Redis caching was much faster.

Fun fact, Elixir's response time went down under regular load.  Turns out in one-shot tests Elixir was doing some unloading and clean-up under the hood.  The continuous load kept the modules in memory, increasing performance even more.

Wait, what?  Sustained load on NodeJS hits a wall and slows down, and Elixir gets faster?

## Doing some metrics

I pulled the two clones I cared about apart: the NodeJS ExpressJS clone, and the Elixir clone, and placed some metrics to time it.

### Request parsing

The url parameters coming into both systems is clearly the same, I was using the same request client in all cases.  But I found that the time to hit the controller and actually handle the request was shorter in Elixir.  In NodeJS I retrieved the parameters and through some string operations broke out the parameters, and decoded that JSON query object.

From my experience using C and C++, I had the opinion that string operations is one of the slowest things you can do in a language.  Just running a tight loop integrating an int, with and without logging each value, has obvious differences in the time needed.

I didn't expect that NodeJS, highly touted as fast, and with direct JSON support, would be slower at parsing JSON input than Elixir.

### Cache Checking

This one wasn't so surprising.  The NodeJS concurrency model will let the C++ code that does its networking run in parallel, but it will incur a delay anyways: the response from Redis must be scheduled back onto the main thread to be handled.  Elixir at this point also had a single scheduler, so it should have had the same problem, but it is written synchronously and the runtime schedules it.  In proper error-handling NodeJS code, you must write asynchronously, doing pieces of work and constantly rescheduling the next step on the event loop to avoid blocking other requests.

Elixir had the benefit of relying on the scheduler: If there were other requests in flight or not, the code looked the same, and just the reduced overhead of not having to code for the concurrency model gave it a boot.

### Database check

After seeing the performance differences in request parsing, and knowing that a MongoDB request was essentially " write JSON request -> send to database -> receive from database -> read JSON response", this was also faster on Elixir.  The combination of faster JSON operations, and simpler rescheduling on the single core, let Elixir keep a healthy lead.

### Response formatting

To try to enable better caching, multiple classes of user would use the same database query under the hood, and just have portions of the response redacted or reshaped to fit their access levels.  Discussions about how crashing the server might have dumped an unfiltered object should error handling not be proper aside, this was the behavior of the production system.

In Javascript, the model was to run through a series of checks and mutate the response object directly.  In Elixir this is not possible.  Instead you are forced to create a view of the data, so you select your view based on scope once, and then create a new response object cherry-picking the appropriate fields for customer return.

In hindsight this is pretty funny.  Our existing code was coupled to SailsJS, which is meant to mimic a MVC, meaning there should be a view doing what Elixir forced my to do, but was bypassed in production.  A sign of the times I suppose.

In any case, faster, yada yada.

## Sanity Check

The numbers I was looking at seemed dubious.  There was no reason for the industry-standard NodeJS language and runtime to be that much slower than Elixir; especially since I had a few years coding for NodeJS, and had picked up Elixir only that week.

I talked to the other engineers, sharing my results.  I checked articles by external engineers, and blogs by companies that tried similar things.  With only a few exceptions, Elixir really was that much faster.

The exceptions I found are are first: Elixir (and Erlang) aren't that great at mathematical performance, don't try to run a physics simulator or anything in it.  Not too bad really if you used c++ interop.  Second, because data types are immutable, if you end up needed to alter very large structures, there are runtime penalties involved.

## Another Meeting

While I was the API heavy hitter, and had a lot of leeway for changing things when needed; I was continuing to cross my Ts and dot my Is, I took the findings before the CTO and Architect officially with a plan:  We would start migrating our API from NodeJS to Elixir, one request URL at a time.

These facts helped immensely:

- The Architect and CTO already thought Elixir had potential
- My numbers were pretty compelling
- Our SAAS server host supported Elixir (not well, no OTP, but at a basic level)
- Our use of the API Gateway meant we could toggle between API implementations easily and directly
- Our production API was really struggling

So it happened.  We would start a new git repo, and start cloning endpoints from the NodeJS API, starting from the highest-demand endpoints.

## Looking back

Not normally present on a blog like this, I am writing this post years after the fact.  So I can share a few things:

1. The migration went very very well.
1. Elixir's support for adapters, encapsulation, and the contract of the interface has made me a better engineer.
1. Elixir's direct and community support for testing, and Mocks, has made me a better engineer.
1. Elixir was much faster than I expected, especially once I knew what I was doing, and started peeling out the NodeJS-behaviors like relying on external services instead of bringing things in-cluster.

This was the start of an adventure with I both enjoyed, and shaped my perception concerning Interfaces, testing, and clean code.  I'd highly recommend this to any Engineer, developer, or hobby coder.