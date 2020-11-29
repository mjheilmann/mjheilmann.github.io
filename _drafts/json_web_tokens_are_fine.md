---
layout: post
title: "Json Web Tokens (JWT) are fine" 
---

This post is about JWT, auth methods, and why these tokens are just fine.

1 - yeah, dont put sensitive info in there, but a users userid in your system should not be sensitive
2 - you can trust them, if a user can hack a JWT, you have bigger problems
3 - they should be SHORT_LIVED!  if you want one weird refresh system issue a token just for refresh or something
4 - they are fantastic for password reset emails:  dont track them, and let them expire.  No table holding pending resets or anything
5 - no cookies, no cookes, and dammit, no cookies!  mobile clients and web pages can store a token directly, where your javascript can check and delete manually instead of stupid cookie workflows