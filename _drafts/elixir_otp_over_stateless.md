---
layout: post
title: "Elixir: Stateless to Stateful OTP" 
---

sister port to node vs otp, but this post is getting the nuts out for an implementation.

driving forces:
1 - very high throughput endpoints, data cached for a few seconds becomes stale quickly
2 - caching post-specific data that is a bit slow to query (for our user count)
3 - caching user-specific things for fast access while the app is open

- Elixir - transitioning from a user-cache NodeJS-style API to a real-time, server-caching actor model


Step 1: the server nodes have to be able to talk to eachother!
otp secret
otp clustering (all-to-all or segmented?)