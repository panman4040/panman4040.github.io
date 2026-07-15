+++
date = '2026-06-11T13:59:16+08:00'
draft = false
title = "Method-based access control can be circumvented | <span style=\"color:#2ecc71\">Apprentice</span>"
tags = ['training', 'PortSwigger', 'writeups', 'web-exploitation']
description = ''
showLastUpdated = true
+++
*Took too damn long tinkering with Burp while in fact I can just paste the payload into the URL*

We are given the choice to first log in the administrator account to see what's behind the scene. While we are changing `carlos` privilege, we can intercept the request:
![](/images/7dac5a5c-7e49-4b38-a390-fb8eb8c54c45.jpg)
Now we know that `/admin-roles` is the endpoint for changing perms, with `username` and `action` as its params. However when we attempt to send the POST request as `wiener`, we will be hit with Method Not Allowed.

So the intuitive solution to go from here is *try* to change POST to GET, the simplest way to do so is just paste into the URL. Surely enough, the web app's filter only weeds out POST request, while allowing GET to pass through uninterrupted. And voila we have solved the lab.