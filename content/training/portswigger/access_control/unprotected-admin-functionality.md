+++
date = '2026-06-02T14:43:16+08:00'
draft = false
title = "Unprotected Admin Functionality | <span style=\"color:#2ecc71\">Apprentice</span>"
tags = ['training', 'PortSwigger', 'writeups', 'web-exploitation']
description = 'My first lab!'
showLastUpdated = true
+++
Just as the name implies, we are set out to look for an admin panel. Simply access `robots.txt` that they convieniently left for us:
![](/images/unprotected-admin-functionality.jpg)
From there we can delete the `carlos` user from the server. 

Besides accessing `robots.txt`, we can also utilise the Site Map feature of Burp Suite as follow:
![](/images/unprotected-admin-functionality2.jpg)