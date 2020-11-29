---
layout: post
title: "Nightmare: The Truth about NodeJS Scaling" 
---

Nodejs doesnt scale.  You just run multiple behing a load balancer.  This has the effect:
- anything stateful must be external to NodeJS
- an adopted service can find itsself scaling high if it is suddenly used a lot
- NodeJS performance os pretty OK, but due to no threads, you can only actively service N connections, where N is the number of NodeJS instances.  All other connections are blocked/waiting.  A fast front-end or fast back-end can get bummed up waiting for its turn.

Solution?  NodeJS is for microservices for a reason! it splits the load.  Any super choke point on a server should be heavily cached or in something else.