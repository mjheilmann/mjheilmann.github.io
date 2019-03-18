---
layout: post
title: Introductions
---

I faced a common problem this morning, but not in a place I would ever expect:  Homebrew.  There is a storm of negligence that came to a head with me unable to run any of my production APIs.

The problem?  _Homebrew's default behavior in 2.0_

# My Broken Dev Environment

You see, I had several things align that resulted in my inability to run my production code locally:

- Recommended behavior in Brew is to keep packages updated, running `brew update` and `brew upgrade X` often
- Brew keeps older packages, so if you have legacy code, you can `brew switch` to go back and run it (note this is not pinning, as I want both versions depending on my context)
- Brew released 2.0 last month, which is a major upgrade, introducing the behavior to run `git clean` for you
- `git clean` will remove all of your old unpinned packages
- `brew update` had no warning or blurb about this change.  Reading online has "its in the release notes" as the accepted answer.  See the notes [here](https://brew.sh/2019/02/02/homebrew-2.0.0/)

- Elixir unpublishes wither old packages in Homebrew
- My production code requires OTP 20 due to a TLS change that I haven't had time to address

So, Here I am Monday morning, unable to run my production scripts as my workstation now only has elixir 1.8.1 and OTP 21 installed.  I believe that brew should not have updated int hat fashion without having a message or blurb.  Or more importantly, that change should not have been made.  It appears that the new behavior was introduced so some vocal users will stop complaining about brew taking disk space.  Instead of having those users use the established commands to remove old packages, they broke my workstation.

This would have been less of a problem if I could simply reinstall the old version and `switch` to it, but the older versions are unpublished by the Elixir team, which is another issue in and of its-self.

# Docker to the Rescue

2 years ago I worked full-time on a linux workstation, using the vanilla package managers.  One strategy I adopted in dependency management was to use Docker containers for local dev.  I didn't have to learn any non-standard package management for whatever language I was using at the time, and I was already building docker containers for deployment.

I hit the usual endpoints, Google Keep, Evernote, and regular searches, but I did not find anything redescribing this development flow; so I reconstructed a simple case and will cover it here:

What you want to do is to have a docker image ready that is able to build and run your project, but you are not dockerizing your application.  My command looked like `docker run -it --rm --name elixir-1.6 -v "$PWD":/usr/src/app -w /usr/src/app elixir:1.6 bash`

For anybody less familiar with docker, the broken down command is

|Segment|Meaning|
| ------------- | ------------- |
| docker run | Tell docker we are doing something new, and a container will be created |
| -it | We want the container to be interactive, so we can use it manually |
| --rm | Don't keep the container if we exit it |
| --name elixir-1.6 | Name the container so we can recognize it if its not removed |
| -v "$PWD":/usr/src/app | Take the current working directory, and mount it inside the container so it is available (2-way) |
| -W /usr/src/app | When we enter the container, start with our mounted source code |
| elixir:1.6 | This is the image we are running |
| bash | This is the command that Docker is running for us interactively |

# Results

This almost worked like a charm.  That image does not have `vi`, `vim`, or `nano` installed, and it had to initiaze both rebar and Hex.  Editing files is a snap as the locally mounted folder is available to yuor editor or IDE of choice.  Adding dependencies is as easy as tagging a new docker image to launch from.  I ran my mix tasks, and captured for later (both in my notes, and on here).

If Docker is already part of your flow, this is a simple trick for managing dependencies that you are already using for production builds.
