---
layout: post
title: "Elixir: Everything I want in testing" 
---

I found my happy place with the support, community, and testing in Elixir.  I write in many languages, and beyond my internal resistance to slow down and interface and test everything properly, I don't want to need to compete and select yet another integration to support the level of testing that I need to accomplish.

Before I get into how it helped, I want to plug my latest favorite C++ testing library: [trompeloeil](https://github.com/rollbear/trompeloeil).  This is (so far) a useful framework for getting close to what I'd want.

Elixir forced me to learn a few things that I had either missed, or had simply not come up in my early experience and education.  Honestly, academics and working in research can lead to a lot of bad habits that I am still breaking.

These things are:

1. Replaceable modules
1. Unit testing
1. Dependency Injection
1. Mocking and Faking in test

## Replaceable Modules

What do I mean here?  Lego bricks are replaceable, so are most pieces on a car.  If you are updating your house doors are replaceable, while walls really aren't.  Automotive components, legos, and other things that are replaceable have a well-defined interface.

Legos have a small interface definition that all of their blocks opt into: the pegs on top of bricks match the structures on the bottom of other bricks.  Pieces that are unique have a point that adapts to the common interfaces.  Very few, if any, pieces break this pattern, making lego pieces _very_ replaceable.

Car parts are more restrictive.  A lot of parts from say a Subaru wont fit onto a Ford.  Some might, their windshield wipers or batteries might share a common external specification (interface?), but seats, body panels, and other things are not compatible.  This is still fine, each automaker has their own interface definitions that they define.  If you find a part that matches the specification for the vehicle you are working on, the pieces are replaceable.

I say that house walls are not replaceable in this definition.  Sure walls have a general shape and a vague definition according to building codes, but to change or update the structure of a wall, you are potentially remodeling the entire house.  This is no longer replaceable as you are refactoring and changing other components to support the change.

We want our application to be more like Legos.  We present a defined interface for other code that uses our module.  We also consume, (or adapt and present) interfaces for pieces that we use.  Our logic is sandwiched between these two interface boundaries, and tries it's best for the interfaces to be generalized.

## Unit Testing

Sure, lots of applications and frameworks have something they call unit testing.  but if you take a peek at a simple definition from [Wikipedia](https://en.wikipedia.org/wiki/Unit_testing) unit testing is low-level testing in isolation.

A free function with zero dependencies (maybe a math utility?) is trivially unit testable.  A module that calls that free function?  Or other modules? Thats not trivial anymore.

Ignoring the corner case where a module is calling a trivial free function, generally a module shouldn't need to know how a module it is calling is doing its logic.  I'll say that if your module knows how/why a dependency is doing its work, then it's not a module dependency, but a larger malformed module.

If you are not properly isolating your module from its dependencies when you are testing, you are not writing unit tests.  Unit tests are isolated pieces of logic.  You are simply writing malformed and mislabelled integration tests.  Stop that!  But how?

## Dependency Injection

In languages that are missing some of the dynamic nature of Elixir, you use dependency injection to make things replaceable.  This is the pattern of removing hard-coded dependencies within a class or module, and then when you use that module you provide those dependencies (or interfaces to them).

This can also be referred to as making your code compose-able.  In C this can get messy, or devolve into some sort of global state monstrosity without careful planning.  In C++ this is easier, where you can pass pointers to the constructor of your class, literally "here's that thing you need".

Once you are able to provide the dependencies to your code instead of having hard-coded values, your on modules are more versatile.  A module built this way depends on a few interfaces instead of implementations, and you can swap the implementations at any time without modifying your code -- so long as the interface holds.

If your dependencies do not have their own interface, or the replaceable parts do not have the same interface, use the adapter pattern with your own interface definition, keep things neat and don't let other developers' code dictate your code quality.

## Mocking and Faking in Test

Here is the real winner.  Outside of something like unit testing a database layer, where you can spin up a Postgres instance and wrap each test into a transaction; this is how you get your unit test coverage.

I have seen C++ codebases where the modules were not interfaced or had their dependencies managed.  To try to get their code coverage they were subclassing and creating custom code just to enable their testing.  They (sometimes "we") had tests that were integrations instead of units, and we could not get full coverage due to hard to replicate situations.  This takes time but is fixable.

In a codebase that has interfaced modules, and the dependencies are managed outside of the module in test, you can get full unit test coverage using Fakes and Mocks.  A Fake or Mock is usually a framework that allows you to both A - assert what parts of the dependencies are or are not called, and how; and B - present specific responses, results, and apparent behaviors from the dependency into your module.

One does not try to force a network timeout to ensure your module handles one correctly, but instead Mock the networking library and force the response directly.

At each layer of your application, you use Fakes and Mocks to isolate it from the layer below it, and unit test the module to your hearts content.  As each layer honors it's interface, it works out a lot like a mathematical proof:  Get unit coverage at the base levels (0, 1), and then at each level you can assume that the other layers are fine, and just be concerned with the current layer (n, n+1)

## And What About Elixir?

I haven't talked about Elixir yet, and those earlier points are general to any software development.  They play into SOLID principles pretty nicely, which I wasn't referencing directly when I was learning/relearning these things.  But I'll briefly talk about a few things that led my back to these engineering truths:

Elixir is young and opinionated.  Elixir is a young language, only appearing around 2011.  I won't rehash its origin story here, but it was written very purposefully for use in real systems.  It hasn't had the time to age and splinter as other languages have.  It also has had a lot of the hooks for testing and documentation present up front, so instead of users writing their own testing frameworks there is better community support for the few, or single, options present.

That could be a negative, as there are not many packages present, but as the language was pushed to be concurrent, testable, and documented up-front, unit test coverage, performance characteristics, and code organization are all up-front.  The language documentation discusses how to adapt and interface your modules, and includes best practices for dependency injection.

So imagine me: A NodeJS/FullStack Engineer who is transitioning to Elixir, fresh out of re-evaluating the NodeJS testing frameworks while looking at some low-quality code, and I see:

1. Write your module like this
1. Organize your functions like this
1. Reference your dependencies like this
1. Here is where your unit tests go
1. Here is how to do advanced testing
1. And Here is how to track and improve it

It was mana from heaven.  I'm all for freedom of choice and expression, but having a "no, do it this way, this is better" supported strategy at the community level was very guiding.

I have heard it said, and I agree, that leaning a pure functional language like Elixir will help you change your way of thinking and make you a better developer.  But I'll say that learning Elixir has brought me love for well organized and testable code, and I've taken those lessons back to Python and C++ in later jobs.

## The Warm Blanket of Good Test Coverage

Having good test coverage, supported by good requirements specifications, good testing frameworks, and code written to use them both, is a cozy warm blanket.  I have been on a product where the best I could do was think positively, the cozy blanket of test was missing, and I wouldn't really know if the problem was fixed, or if new bugs were introduced, until much later in the deployment pipeline.  It was stressful, and fed my anxiety.  Converting into well-tested code, and leading your bug-fixes in testing-driven development, is a true source of confidence that your code is doing what you want, how you want it, and only those two things; especially as you are changing other parts of the code.