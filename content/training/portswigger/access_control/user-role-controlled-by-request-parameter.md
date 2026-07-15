+++
date = '2026-06-02T15:00:16+08:00'
draft = false
title = "User role controlled by request parameter | <span style=\"color:#2ecc71\">Apprentice</span>"
tags = ['training', 'PortSwigger', 'writeups', 'web-exploitation']
description = ''
showLastUpdated = true
+++
We can simply intercept the request to `/admin` and change the cookie as follow:
![](/images/0b2bf73e-c606-4bf4-ad4f-e673d0c0ba56.jpg)
After that, we can delete the user `carlos` and solve the lab.