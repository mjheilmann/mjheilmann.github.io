---
layout: post
title: "Nightmare: Coupling and Optimization" 
---

OpenSim -> Halcyon switching.  Halcyon did a lot of correction and optimization in th codebase, performance is fantastic.  Halcyon has optimized out of being cross-platform (they only run on windows), and now all work on rolling the sim into a kuberneted cluster is halted.  Literal hard-coded implementation dependence on windows-only .Net libraries

Some of these things made Halcyon better (for them) while making it worse (for us); they implicitly broke the cross-platform capability that we had been using from thir pre-fork codebase for years.