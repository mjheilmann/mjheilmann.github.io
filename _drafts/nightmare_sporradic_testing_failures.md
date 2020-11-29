---
layout: post
title: "Nightmare: Sporradic Testing Failures" 
---

A good unit test is isolated, only checks one thing, and fails in clear and predictable ways.

This is to be discussing sporadic unit tests that I am dealing with, and what it looks like.  This will also discuss what the actual fix is, after rejecting and failing enugh patches and band-aids

reasons:
1 - unit tests were touching things like the network and filesystem, not ideal in any unit test
2 - even dockerising all test, the FS test would fail sometimes, still dont know why
3 - the "isolated" networking tests can fail to bind on the networking port
4 - is CICD node runs more concurrent builds and tests than it really should, just massive resource contention maybe?