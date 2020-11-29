---
layout: post
title: "Elixir: My First Steps" 
---

This post is discussing my path into elixir:  reading tutorials, working through things.  This is moved heavily by my target deployable:

1 - a node-js equivalent, stateless API
2 - Phoenix framework
3 - Phoenix auth and validation
4 - Phoenix controllers, and rendered responses
5 - working out the pipeline to decode a query, map it to mongo, fetch the information, and map it back out
  - our database contained a lot of information that is redactd if you are not paying.  We used templates instead of just dumping json, so we dont leak internal information, and we had a template switch based on user / customer / hero to redact the information as needed.  This let all the endpoints cache and share the same queries for a minor overhead
6 - What does immutability mean in elixir
7 - this is my first honest true functional language, neat!
8 - talking with genservers
9 - writing synchronous code that can just fail/crash, and letting Erlang do its thing