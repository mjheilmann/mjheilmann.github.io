---
layout: post
title: "Elixir: From Node Scaling to OTP" 
---

This post is discussing the tradeoffs, and architectural changes when moving from a NodeJS style of service on an isolating host like Heroku, to an OTP cluster.

Hint:  Session and user-specific caches are not held in a global cache anymore, but spun onto a GenServer.  The genserver takes care of its-self, including timeout and purge.  The genserver is named on a distributed registry (delta crdt anyone?), and a web-serving elixir node will lookup and fetch against the genserver.  If your closter is not distributed, then a local lan fetch can be faster than a saas db fetch.

If your cluster is distributed, then you can look at caching in a localized cluster, and turning it from a global cache into a localized cache.

a genserver per user can cache very user-specific things, like a holding pen of upvotes, downvotes, and post totals while a separate process syncs that information down to the database.

a genserver per user is not that crazy, if you want to do real-time messaging to users, and now have a socket/endpoint per user anyways