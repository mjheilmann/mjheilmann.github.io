---
layout: post
title: "Elixir: Absorbing the Ruby Suite" 
---

This post is talking about the hero portal, which was written in ruby.  Specifically, it needed some help, and we didn;t have any more ruby developers.  Since it has a web front-end I can scrape calls from, and Elixir supports microservices within the runtime, I took a few days and reverse engineered, and implemented the ruby portal into elixir.

1 - create calls and matching behaviors from ruby back-end
2 - re-hosting the html and such from the ruby code to elixir
3 - retiring the ruby service and its separate servers.