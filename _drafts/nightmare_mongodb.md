---
layout: post
title: "Nightmare: MongoDB" 
---

it might be different now disclaimer, this is true when I was at fanhero

1 - schemaless is a bad thing
2 - no schema means no validation
3 - performance problem?  index all the things!!!
4 - clustering?  hope you have time to rewrite all of your queries! (the mongodb "join" isn't shard aware)
5 - transactions: nope.  was "coming" for the bleeding edge, but dont run bleeding edge in production!!
6 - complex queries are in javascript, and can take forever to complete
  anecdote: I had a query to update a few million rows that was taking forever and kept timing out (mongo provider capped at ~5 minutes, so salt here).  Rewrote to do the check locally (meaning read from remote, compare, write if updated) locally in elixir, done in a minute.
7 - schemaless meant no transportable schema, test server and production server had no guarantees that they were the same.  Whomever originally suggested cloning production data onto a test server to test migrations should be shot (real user emails?  What could go wrong! real push-notification ids!  yes, we sent push notifications to real users when working on the testing side)
8 - mega document, de-normalize everything into massive single records?  But smaller records promote faster reads.  Index and filter server-side?  Is that the slow javescript coming out again?

The technical debt associated with this model!!!  you are forced into either (and often all of)
1 - running heavy full-update queries on the DB to keep everything in sync
2 - maintaining backwards compatibility in all read/write code in case you hit an older record
3 - a state of perpetual prayer to the schemaless ad-hoc database gods that you dont have to spend an hour querying, finding, and fixing mishapen records on the DB that day.

disclaimer: yes we were using an ORM, but we were using multiple languages, so things got messy