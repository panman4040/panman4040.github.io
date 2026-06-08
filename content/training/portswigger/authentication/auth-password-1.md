+++
date = '2026-06-08T13:34:16+08:00'
draft = false
title = 'Username enumeration via subtly different responses'
tags = ['training', 'PortSwigger', 'writeups', 'web-exploitation']
description = 'First use of Grep-Extract feature in Intruder'
showLastUpdated = true
+++
Before going into the solution, I just want to say I wasted time eyeballing for the slightest odd-one-out response received time and response length (rip). Turns out there is a nifty feature in Intruder called Grep-Extract. This feature will extract any useful info of our choosings from responses into the attack table.

Firstly, we shall use Grep-Extract to harvest pieces of data from our responses, including cookies, tokens, error messages and so on. This time we will highlight the `Username or password invalid` text
![](/images/617b190e-f145-4c74-a7b5-28cee70b9c77.jpg)
Notice that there is *one less dot* from `akamai`, turns out that's our valid username. After that, we just proceed with password brute-forcing normally. Very nifty.
