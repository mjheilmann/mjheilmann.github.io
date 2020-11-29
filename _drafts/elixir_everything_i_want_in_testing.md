---
layout: post
title: "Elixir: Everything I want in testing" 
---

Testing, Mocking, shaping modules (I don't like direct dependency injection, I used instead ENV flags to swap implementations in test)


high performance, and recommended, adapter pattern.
1 - write module, hooray!
2 - establish interface module, calls functions internally, exposes only functions user needs, adapting our module for use
3 - users: write adapter module against our interface, with our interface as the default implementation
4 - replace the interface for test INSIDE YOUR OWN ADAPTER MODULE! keep control over how the Mocks are introduced, and keep mocking and dependency injection out of your user interface (as much as possible)!

Mocking, testing, and test coverage strongly encouraged, and enabled, by the language authors