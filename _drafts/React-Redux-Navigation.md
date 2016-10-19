---
layout: post
title: React Navigation with Redux State
---

I may have mentioned before, but I like to keep things simple.  Simple for the developer, that is.  If there are extra steps the library developer can take to make their library simpler for the consuming developer, they should be taken.

I had a run-in with React Navigation.  The navigation libraries I found were not what I wanted at all.  I could not pass props to my components.  I could create a provider for my stage object to pass it around the navigation component, but then I had to wrap all of my navigable components in order to receive the state object.  For me, that is unsatisfactory.

So, I wrote my own.
