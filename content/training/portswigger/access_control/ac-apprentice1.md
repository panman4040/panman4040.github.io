+++
date = '2026-06-11T09:38:16+08:00'
draft = false
title = 'Unprotected admin functionality with unpredictable URL'
tags = ['training', 'PortSwigger', 'writeups', 'web-exploitation']
description = ''
showLastUpdated = true
+++
Very straightforward, the lab introduces a vuln where sensitive endpoints are exposed in plaintext, in this case some JS scripts:
![](/images/1687fb5e-12ca-42cc-8de9-c45dc0722ced.jpg)
Simply access the hidden endpoint and we can solve the lab.