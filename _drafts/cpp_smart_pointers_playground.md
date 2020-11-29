---
layout: post
title: "C++: Smart Pointer Playgound" 
---

- Smart pointers, normal usage, and getting freaky with lifecycle management (weak pointers on the service with strong pointers in the user handle)


C++ is an old hat, but it knows a few tricks.  lets go over smart pointers.:


1 - shared pointer const - the self-destroying copy

2 - the unique pointer - this thing

3 - weak pointers....wait

observable system:
user holds a handle that is the shared pointer, service holds weak pointers.  User instance cleanup is transparent within the system and a held / remembered de-register call is no longer needed, neat!

socket-concurrency model:
user holds weak pointer to their socket, they can read and write if it hasn't been destroyed.  System holds shared pointer for the actual resource, allowing for orchestrated cleanup.  No service hanging on shutdown because a user didn't close their resource handel correctly.