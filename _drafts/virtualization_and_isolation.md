---
layout: post
title: Virtualization and Isolation
---

- Virtualization Adventures: ProxMox

Why do this?
1 - isolation.  Try running two applications on the same host with conflicting dependencies :(
2 - resource management: running multiple things isolated on one big machine instead of a lot of small machines

Docker isolation
LXC Isolation
Full Virtualization

linux can do containers
windows tries to do containers for windows images
OSX and Windows do docker "transparently" in a vm

Isolation of things that dont do well in docker:
- Windows containers
- backup management and virtual machine mobility

Rolling my own cluster, the cheap way:
ProxMox