+++
date = '2026-06-11T15:50:16+08:00'
draft = false
title = 'Referer-based access control'
tags = ['training', 'PortSwigger', 'writeups', 'web-exploitation']
description = ''
showLastUpdated = true
+++
We are given the option to try out admin perms for ourselves. When we try to upgrade `carlos` credentials, we can intercept the request and see what's behind the scene:
![](/images/a9a9f1d4-ae2f-4938-95ad-576445e88aba.jpg)
Unlike the previous labs, the request to elevate one's credentials is simply a GET request. If the web app accepts `Referer` arbitrarily, we can simply change the session cookie to ours and solve the lab.