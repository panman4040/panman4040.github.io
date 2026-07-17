+++
date = '2026-07-16T16:17:16+08:00'
draft = false
title = "SQL injection with filter bypass via XML encoding | <span style=\"color:#3498db\">Practitioner</span>"
tags = ['training', 'PortSwigger', 'writeups', 'web-exploitation']
description = ''
showLastUpdated = true
+++

This lab contains a SQLi vulnerability in the stock check feature, in which the results from the query are returned in the application's response. So we can probably use `UNION` attacks to retrieve data from other tables:
![](/images/viber_image_2026-07-16_16-20-05-703.png)

Let's try inserting a `UNION SELECT` to see what's up, just for fun:
![](/images/viber_image_2026-07-16_16-22-12-133.png)

Looks like there is a WAF in place that filters out possible injection attempts, which is kinda bad. But is it bulletproof? Can it detect an injected query that is encoded once? Twice? Thrice? For this lab, I will be using the Hackvertor extension which is a Swiss knife for everything data, decoding, encryption, encoding, the whole shabang.

But we first need to verify whether the queries are processed **synchronously** or not. I will try inserting sleep payloads of different database syntax to see which type the application is using, in this case it's PostgreSQL:
![](/images/viber_image_2026-07-17_09-48-47-854.png)

The response takes more than 10 seconds to be sent over, meaning our injected query is processed normally. It's now trivial to determine the number of columns, then the table names, column names, and data exfil. You can see the progress below:
![](/images/viber_image_2026-07-17_09-51-13-607.png)
![](/images/viber_image_2026-07-17_09-53-18-889.png)
![](/images/viber_image_2026-07-17_09-53-50-499.png)
![](/images/viber_image_2026-07-17_09-59-35-463.png)
