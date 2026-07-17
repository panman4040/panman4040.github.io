+++
date = '2026-07-15T08:41:16+08:00'
draft = false
title = "SQL injection UNION attack, finding a column containing text | <span style=\"color:#3498db\">Practitioner</span>"
tags = ['training', 'PortSwigger', 'writeups', 'web-exploitation']
description = ''
showLastUpdated = true
+++

The problem statement hints at the filter functionality has an SQLi vulnerability, and similar to [last lab](/training/portswigger/sqli/sqli_practitioner1) we have to find how many columns are there first, then find out which column holds string data.

Finding the number of columns is trivial, either use order by index or union select with `NULL`:
![](/images/viber_image_2026-07-15_08-45-26-423.png)

We now know that there are 3 columns in the table. However our goal is to make the backend returns a certain canary, but we don't know which column is compatible with string data yet. This is quite simple, all we have to do is place some string value into each column in turn:
```
' UNION SELECT 'a',NULL,NULL--
' UNION SELECT NULL,'a',NULL--
' UNION SELECT NULL,NULL,'a'--
```
![](/images/viber_image_2026-07-15_08-46-34-716.png)
Now we know the second column can hold strings, now it's cake walk to get the backend to return our canary:
![](/images/viber_image_2026-07-15_08-50-22-685.png)
