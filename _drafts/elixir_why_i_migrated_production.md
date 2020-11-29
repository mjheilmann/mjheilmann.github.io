---
layout: post
title: "Elixir: Why I Migrated Prodution (and how)" 
---

discussing what I did at fanhero.

reasons:
1 - sails.js + our node code is terribly slow, and terrible.  I helped.
2 - express.js and node directly were faster, but a complete rewrite off of the framework is already hard
3 - if we just rewrite nodejs, how do we isolate ourselves from the older code and decisions made there?  The buiness side can demand faster work and the temptation to copy-paste will be there
4 - 2 engineers had been using and loving Elixir, so I included it in the performance testing

performance metrics (that I remember)
risk/benefit trying elixir (small company do normal thing right?  But we were small enugh that 3 people knowing elixir was half the development team)
