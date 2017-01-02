---
published: false
---
I have three daughters who - thanks to my father-in-law's connections - each has their own gently used iMac.  They like boardgames, and a few local games.  But they also like to play games like agar.io, and Minecraft.  So I let them.

I let them play, I don't give them open internet access.  Up until this point I have been utilizing the parental guidance feature in OSX, using an internet whitelist to allow access to the minecraft auth server, agar.io, pbs kids, and other child-friendly sites.  But I noticed a growing problem, and its name is Minecraft!

My girls didn't know to inform me, so I don't know on which Minecraft update it occurred, but Minecraft now utilizes a long and seemingly endless rotating list of ipv6 addresses to communicate with to allow for aauthorization.  I have sat in front of one of their computers just relaunching minecraft and whitelisting every ip Minecraft is attempting to access, and they still cannot logon.

I'm sure the reasoning is to prevent piracy or something, but that hasn't stopped it from shooting me in the foot with 3 paid accounts.

Now, my girls are young, and they play mostly LAN maps, but this has already prevented then from loading their skins.  I have come to discover that custom skins and appearances are very important to young girls!  But that aside, I am from a large family, and my daughters have many cousins that they are wanting to play with.  Since I can't fix Minecraft and force them to stop hard-encoding ip addresses into their clients, I'll have to adapt myself, and my little network.

## The Fix

So, what can I do?  I like the guided mode on OSX, it keeps the girls from changing their computer settings, or looking up swears in the dictionary, but the internet filtering is not working for my use case.  The fix is... Open up the internet access on the machines, and make another machine on my network perform the filtering for me!

I like my unfettered access to the digital realm, so I'm not going to use net nanny or a filtering proxy on my network gateway.  I want less work, not more, so I'm not going to start using vlans at home.  I am putting DNS whitelisting into use.  I can use the girl's machine's mac addresses to feed them a different DNS server than my machines, and from their dns resolver I can restrict their internet access.  Any new device has open internet access, but these iMacs will get a domain-denied if they go somewhere unapproved.

But what if they look up the IP address of a web page and visit it directly?  Well, theres not much I can do about that.  But then again, I can't stop them from going to the library and using the computers there either.  I'm not a tyrant, I am not trying to control them, I'll just do my part and ensure their machines in my house are safe for them.  If they get to the point of rooting their machines and modifying the network settings to use an external DNS server, they deserve it; but I'll be watching.


## How does this work?

## TL;DR

