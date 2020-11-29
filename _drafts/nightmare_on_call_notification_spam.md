---
layout: post
title: "Nightmare: On-Call Notification Spam" 
---

This is not about filtering or configurint your on-call system correctly, which is super important.  A debouncer may have fixed this.  Better scaling might have fixed this.

The core problem here:  The platform is too slow to handle minor traffic spikes directly.  The auto-scaling was not fast enough for the quick and unpredictable nature of our spikes.

spitballing:
1 - Since we have hooks for our content creators, we could pre-scale the servers on a push.  Not done, only a few creators initiated spikes, and specializing a platform for a few is bad taste
2 - Rate-limit our notifications to flatten the spike.  We did this anyways for some unreliable push notification integrations, but some evens were still spikey.
3 - Do some benchmarking and performance analysis on the servers.  Nightmare fuel.  Sails.js had a hard-coded sleep in some actions....for why?  Raw node.js wasn't much better.
  - move to AWS serverless or google solutions (read: expensive)
  - re-evaluate the backend platform / language for performance (architect used and liked elixir, we were scared bad javascript wouldjust get copied over if we stay on node.  force the "right thing")