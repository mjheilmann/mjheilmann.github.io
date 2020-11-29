---
layout: post
title: "Elixir: My Caching Strategy" 
---

- Elixir - using interfaces and structs to cluster and cache all in-platform (translate query and graphql into a struct for an "internal query", which we pass through our own cache system, hitting the DB on a miss)
