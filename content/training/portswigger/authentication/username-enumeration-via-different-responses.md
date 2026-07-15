+++
date = '2026-06-02T15:23:16+08:00'
draft = false
title = "Username enumeration via different responses | <span style=\"color:#2ecc71\">Apprentice</span>"
tags = ['training', 'PortSwigger', 'writeups', 'web-exploitation']
description = ''
showLastUpdated = true
+++
Using the provided username and password wordlists, my initial solution is to simply run a cluster bomb attack in Intruder to test all permutations possible. But on the Community version this took an excruciatingly long time because of the rate limit.

Another way to do this is to perform a sniper attack to enumerate only the valid usernames first, and from there we have a much shorter list of usernames to check their passwords.

![](/images/7bf7b64c-6975-474c-8409-af6da7af08a6.jpg)

Notice that `ak` has a *slightly longer* length than the rest, we can confirm this is the only valid username by typing into the login form.

![](/images/ad9ed5d1-8253-4431-a933-b09acb7f83ce.jpg)

`111111` has a significantly shorter length than the rest, and it is indeed our password for `ak`. Very fun problem.