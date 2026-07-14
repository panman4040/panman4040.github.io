+++
date = '2026-07-14T14:19:16+08:00'
draft = false
title = 'SQL injection UNION attack, determining the number of columns returned by the query'
tags = ['training', 'PortSwigger', 'writeups', 'web-exploitation']
description = ''
showLastUpdated = true
+++

The most glaring functionality present is filtering products by category, as shown here:
![](/images/viber_image_2026-07-14_16-06-18-933.png)

As reflected in the lab's name, the first step of a UNION-based SQLi is to determine the **number of columns**. We can either union select by index or select by `NULL`, whatever floats your boat.

Note that `NULL` works because it's convertible to every common data type, thus makes our payload more likely to succeed.

Assuming there is no WAF nor client-side filters in place (which there aren't), we can apply our elementary payload:
```
` UNION SELECT NULL--
```
![](/images/viber_image_2026-07-14_16-08-48-708.png)

Though the request is invalid, the server does indeed return a 500, which means it's actually processing the query rather than chopping our payload off. Since this basically confirms the lack of filters, we can keep adding `, NULL--` until the number of columns matches:
![](/images/viber_image_2026-07-14_16-24-34-824.png)
The server now returns a 500 after 3 `NULL`s, corresponding to three columns in the database. Neat!