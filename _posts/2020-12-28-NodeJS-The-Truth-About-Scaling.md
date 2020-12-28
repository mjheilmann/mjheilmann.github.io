---
layout: post
title: "Nightmare: The Truth about NodeJS Scaling" 
---

This article has some opinion mixed with real experience, but for some more background you can check out my earlier post about scalability and parallelism approaches [here](https://mjheilmann.github.io/2020/12/06/Concurrency-Models.html)

## Buzzword: Processing at Scale

There are a few buzzwords floating around, `cloud`, `cloud-scale`, `scalability`, `big data`, which are pretty fun sounding but don't really mean anything on their own.  These are all from - and alluding to - PAAS organizations.  PAAS and SAAS organizations want to take over your scaling and data concerns so you can focus on the business logic.  I have no beef here, some dev-ops and production support _should_ be automated and farmed out for the health and sanity of your engineering teams.

But there is a devil in the details here.  If you no longer need to worry about scaling, or about how your data flows around, you can fall victim to one of these problems:

- The ease of PAAS and SAAS leads you to pick an underperforming language, driving up your hosting costs
- The PAAS or SAAS API make things so much easier, that you don't realize you have hit vendor lock-in
- Your system collects inertia, which when moving will keep moving, but when at a stand-still will resist movement

These are all cases of having outsourced some key aspects of decision making.  And honestly, can you expect a middleware company to make choices that are better for you than them?

## The Easy Path

These problems come from taking the easiest route.  This is a common behavior of startups and small, agile-based development.  Do what is quickest to market, and then we will clean up pain points on the tail end.

This can work fine, and it does work fine, most of the time.  But when it doesn't work fine, you will find yourself looking at a complete rewrite of your platform.  The notion of "now that we are finding success, we can go back and do it right" doesn't apply well to technical products and platforms.

Even if your team is excellent, and they wrote testable code, and things are modular, and your interfaces are well-defined, you will still find yourself coupled to the patterns and practices of the languages it is implemented in.

An early decision to implement in Python Flask, or NodeJS, or PHP will often limit the organization in the future.  For simpler comparisons, lets restrict this to NodeJS.

## NodeJS does not scale

NodeJS is not a scalable language or runtime, period.  It is a fantastic tool that enables quick development and rapid prototyping, but unless you are deploying to a web browser you are potentially setting yourself up for failure.

Just like Python, and PHP, and probably other scripting languages, everything that NodeJS does well is a module that was ported to native code.  This isn't a bad thing, but it is one thing:  To get the best performance out of NodeJS, you write as little javascript as possible.

But How do you do that?  There are best practices for this!  High throughput applications that are written in NodeJS use Javascript as a very thin glue layer, farming actual logic out to other services, like Nginx, Redis, MongoDB, etc.  You are writing a distributed deployment with emergent behaviors instead of writing an application in Javascript.

### Coding to the runtime instead of the language

This is very restrictive in a few ways:

First: Resources spent on correct coding and design are effectively wasted, as the logic you are looking to encapsulate are now deployment details instead implemented logic.  What does a unit test look like?  There isn't a unit test for this, everything becomes integration tests.

Second: Your engineers, who were Javascript specializing at the beginning of this journey, are increasingly working with specific products instead.  Moving off of Javascript would have been a big ask, but not you are looking at multiple integrations.

Third:  Because NodeJS and Javascript could not handle these cases directly, you now have additional coupling and costs from these third party services that you are using.  This could be a separate production in a suite such as on Google Compute cloud or on AWS, or it could be an entire new SAAS provider to let you hook into a managed MondoDB or Redis cluster.

I'll pause here, and note that this can still be OK, if you and your team has planned for this.  But its a dafe wager that not all teams have, and they are instead falling into a deployment like this.

### The Bad Path

This is not a contrived example, but I sincerely hope it is a rare one:

1. New startup, wanting to hit the ground running.  Grab a simple language some devs know, lets say NodeJS
1. NodeJS has a lot of packages, so an initial API is stood up.  It is fast enough during test, an even during some roll-out
1. NodeJS has close ties to MongoDB, so why not, gotta go fast.  Common stacks here are MEAN and MERN:  MongoDB + ExpressJS + (front-end) + NodeJS.
1. NodeJS hits its first bottleneck: No matter what you do, it is single threaded.  Take the best practice here, and deploy multiple in parallel, with a common front-end to load-balance between them, and the common database between all instances.  There are NodeJS hooks for this, but I'd personally use Nginx instead
1. You can now support multiple cores, which is great!  On a 4 core system you could have 4 different Javascript functions running at once, a 4x increase!  4x isn't that great though, and now that you are starting to benchmark things more closely...
1. Start Caching!  On Google compute and AWS you can do some edge caching, but they don't do well on user-specific responses, which are easy and desirable for a slim front-end.  Instead of caching the API responses, you can cache the database responses!  Add a Redis instance that your API can check before any MongoDB call, fetching the results without hitting MongoDB for a speed bump
1. Things are speeding up now, you are optimizing your DB calls, doing some MongoDB scripting where possible, to speedup anytime you hit the database.  The relatively slow DB calls are helped by your Redis cache, so the API is pretty responsive, but your API is successful.  You need to go bigger
1. You...what do you do here?  You probably didn't integrate with Mongo to enable Sharding, so you just turn the crank and scale your MongoDB instance vertically; this gets expensive and has an upper limit.  Redis can cluster, so spin that up too, but you quickly find the crux of the issue: Every open connection to your API is held by NodeJS, turn that to 11 as well, increasing the number of instances there.

I won't mince words, this can work.  I've also seen it not work.  Without planning and foresight, this is a gamble.

I was on a product using MongoDB, Redis, and Heroku to host our NodeJS Application.  So we had a medium-large sized MongoDB SAAS service, a clustered Redis SAAS instance, and with heavy testing for optimization, a 200 instance Heroku PAAS service, with each instance having 2 cores.  The 2-node heroku boxes were burstable, and our traffic was very spikey, so this was our best achievable performance.  What were we dealing with?  200 instances is the upper limit on Heroku. at least at the time.  The Redis dashboard was not indicating problems.  The MondoDB dashboard was not indicating problems, though we had so many indexes setup it was hard to determine what our real memory usage was.  Heroku was flagging and dropping traffic left and right even at max scaling.

What do you do?  What did we do?  The talks with Heroku to increase our limits were a bit intractible, even at that scale we were not handling the connection throughput that we needed for our traffic patterns; and this is ignoring the slow-to-scale problem that these PAAS provides run into.

We bit the bullet, and migrated off of NodeJS.  It hurt, but at the end of the migration we went from 400 ( 200 2-core - 400 CPU) NodeJS instances to to 4 instances (4 4-core 16 CPU) running Elixir on the same traffic patterns, with the same Redis SAAS and the same MongoDB SAAS instances.  A brute-force direct-replacement out of NodeJS onto a more scalable language, changing nothing else about the deployment, reduced our Heroku hosting costs by more than half, and our system was more responsive.

We were able to do that, as I and several other engineers there also knew Elixir, and could make the jump.  If that was not an option, jumping to Kubernetes deployments, and AWS serverless would be options, where you can go much higher than 200 instances of NodeJS, but I would not call that the best solution.

But what about microservices?  A NodeJS best practice is to split your API up into smaller chunks, to reduce the chances of needing to scale anything to that level.  I say thats pretty cool, but is a workaround.  On this product, it was a single endpoint, a single microservice, that was getting hit and being scaled: the feed.  This was a social platform and the entry-point of the application would load up the feed of content.  We had a Redis cache, and an AWS cache, with AWS serverless session validators, and it still wasn't enough for our use case.

## Anecdotal Evidence

This is part anecdote from my experience on that scaling product, but do some light googling and you will see the common problem: NodeJS scales very poorly.  Python has the same problem.  PHP is slightly better, but quickly runts into its own issues both for safety, and then the scalability of its runtime.  If you are a Google or a Facebook and can bankroll your own data-centers, you can pick whatever you want, or create your own new languages like Golang to smooth things out.

For the rest of us, if you are deciding how to scale when you need to scale, you are a bit late to the party, and you may end up paying other companies like Amazon to scale you for you.

## Choose your language and platform up-front

OK, so all of this is a long-winded way to say: Roadmap and plan before coupling into a platform, runtime, programming language.  Velocity may look good when some engineers are unleashed and they just start coding, knowing what your are creating, and then selecting to best support that up-front will pay off in spades when your success comes.

If not, you are rolling the dice on whether your technical stack will become the victim of your success.
