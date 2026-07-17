+++
date = '2026-07-15T09:06:16+08:00'
draft = false
title = "SQL injection UNION attack, retrieving data from other tables | <span style=\"color:#3498db\">Practitioner</span>"
tags = ['training', 'PortSwigger', 'writeups', 'web-exploitation']
description = ''
showLastUpdated = true
+++

The problem statement hints at the existence of an `users` table, with columns `username` and `password` (surprisingly stored in plaintext). So now our job is to fetch its content and log in as `administrator`.

First, we need to find how many columns in the "filter table". Refer to [this lab](/training/portswigger/sqli/sqli_practitioner1) if you are not familiar with the technique:
![](/images/viber_image_2026-07-15_09-13-17-902.png)

There are two columns in this table, which is *quite convenient* for us as `users` also has two columns. What about the data type stored?
![](/images/viber_image_2026-07-15_09-13-41-766.png)
Again, both columns can hold strings which is *even more convenient* for us as the two columns in `users` are also of string data type. Since we know what the columns' names in `users` are, retrieving the credentials is trivial with the following injected query:
![](/images/viber_image_2026-07-15_09-14-20-676.png)