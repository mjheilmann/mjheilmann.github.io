---
layout: post
title: "Elixir: How I learned the Actor Model" 
---

The actor model is pretty dang common it turns out.  Elixir is just explicit about it.

Elixir actor model

Other actor models (python anybody?)

poor-mans actor model using a framework like zmq

note:  GenServer FTW:  anything stateful, or holding connections, or both, Actor!
in Phoenix, there is an actor holding the incoming connections, and spawning routines to do the logic, with a mechanism to send the response back to the actor and hence the socket quite transparently, its nice.