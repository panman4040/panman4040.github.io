+++
date = '2026-06-11T10:05:16+08:00'
draft = false
title = 'User role can be modified in user profile'
tags = ['training', 'PortSwigger', 'writeups', 'web-exploitation']
description = ''
showLastUpdated = true
+++
At first, I keep digging session cookies and HTML source to see whether they contain any references of `roleid` or not, and I stumped. I realised that I hadn't even attempted to change my email to see what happens yet.

So, upon changing our email, we can observe that the response has a JSON object that keeps tab of our username, email, and `roleid`:
![](/images/5ffde0b9-985d-4faa-950f-54e09d44e1e2.jpg)
Simply add `"roleid":2` into the request body and voila we will get access to the admin panel. This is a vuln called **Mass Assignment**