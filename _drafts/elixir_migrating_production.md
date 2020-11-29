---
layout: post
title: "Elixir: Migrating Production" 
---

This post is talking about the actual migration process.  Be sure to mention that this took a few months; and despite very thorough documentation and testing on the Elixir side, the lack of documentation and testing on the Node.js side bit me in the ass a lot.  One undocumented feature:  Sails.js lets you override return codes.  Some Node devs who had left remapped some failing codes that they "couldn't fix" and that didn;t affect the mobile client front-end from 500 to 200, an called it a day.  When I found and reveresed that, our metrics tanked for weeks as I slowly went back through the system.

path:
1 - keeping mongo and redis
2 - since calls had no shared state, leveraging amazon api gateway to switch calls one at a time from node to elixir
3 - move endpoint starting from the highest throughput
  - quick win: response time dropped despite same redis and mongodb instances
  - quick win: dropped number of node standby instances down to ~5, we were typically between 20 and 200 dependening on platform usage
  - any problems, undocumented behaviors, or bugs, we would switch back to node and patch in develop
4 - As last nodejs query point moved, deprecate and remove node code
5 - most of redis cache was not user sessions (we used JWT and user ID directly), so we replaced redis DB query caches with cachex, caching db queries on host.  caches weren't shared between hosts, but the reduced number of hosts running elixir, and reduced latency asking redis, made it a non-issue
6 - begin looking at OTP and global cluster caches (migrating off of heroku)
7 - begin looking at replacing Mongodb with a properly de-normalized postres DB
  - better driver support in elixir
  - portable schemas for development, test, production sync + rollout
  - DB triggers and schema validation / rejection
  - foreign keys! god bless.
  - DB transactions enabling much improved unit testing near the DB layer