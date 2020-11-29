---
layout: post
title: "State Management" 
---

- State Management and Single-Source-Of-Truth: React, Flutter, API :: Stores, Redux, API state

This should be a simple post, but in a DB, with redis, in an API, with clients in Flutter, and React.js, state management and single-source-of-truth get hard, fast.  Most devs I've spoken to dont like state management, and want it to just look like a simple key-value store, well guess what honey.  Its hard, and important.

anecdote:  Flutter app contains two "application" objects with separate navigation trees because why?
Flutter app with state stored outside of the block, some items grabbed the localization change from english to portuguese, and sume didn't.  That was fun to track down.

react.js components, plus react hooks:
- local component state (am I collapsed) vs application state (am I logged in)
- the actual truth:  its always in the DB persistence layer, anything else is just an adapter
- your adapters should recognize that they are not the truth, and are simply expirable caches for content data