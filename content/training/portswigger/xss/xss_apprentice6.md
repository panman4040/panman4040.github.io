+++
date = '2026-06-26T15:40:16+08:00'
draft = false
title = "Stored XSS into anchor href attribute with double quotes HTML-encoded | <span style=\"color:#2ecc71\">Apprentice</span>"
tags = ['training', 'PortSwigger', 'writeups', 'web-exploitation']
description = ''
showLastUpdated = true
+++
After tinkering with the comment functionality, notice that the backend will directly takes whatever inside the website input box and attach it to the `href` anchor as shown here. One thing to note is that the `http:` prepend filter is not present for this lab:
![](/images/7e1d1201-af4b-495d-8be4-f647067eb219.jpg)

So we can simply use the payload `javascript:alert(1)` to solve the lab. Simple