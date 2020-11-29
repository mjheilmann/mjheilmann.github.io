---
layout: post
title: "Elixir: Threading and Deployment" 
---

This post is for the Elixir concurrency model, it can touch on actors, and how to get a basic service up and running.

This can note that instead of doing the executable target-compiled binary (mix release), it was running in a SAAS platform: heroku

This can note that heroku does not support OTP clustering, so this initial migration was not a rewrite of the whole platform, just the nodejs part.  We still used mongodb (shudder), Redis, and other integrations