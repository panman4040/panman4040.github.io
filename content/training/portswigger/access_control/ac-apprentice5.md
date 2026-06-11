+++
date = '2026-06-11T15:33:16+08:00'
draft = false
title = 'Multi-step process with no access control on one step'
tags = ['training', 'PortSwigger', 'writeups', 'web-exploitation']
description = ''
showLastUpdated = true
+++
We are given the option to try out admin perms for ourselves. When we try to upgrade `carlos` credentials, there is a intermediary step that asks us to confirm our decision. Upon interception, we see that this is expressed as a param `confirmed`:
![](/images/799ae45f-6b32-40c0-96d0-be8a41147ba9.jpg)

*Hypothetically*, the web app may see that `confirmed=true` is indeed included in the form parameters, and assumes that the person requesting this has valid credentials because they *cannot have known* that third confirmation step. 

We can then log in as `wiener` and try repeating that same confirming POST request as `administrator`, and voila we now have administrative perms.