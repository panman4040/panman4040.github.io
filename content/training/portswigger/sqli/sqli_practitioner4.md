+++
date = '2026-07-15T09:30:16+08:00'
draft = false
title = "SQL injection UNION attack, retrieving multiple values in a single column | <span style=\"color:#3498db\">Practitioner</span>"
tags = ['training', 'PortSwigger', 'writeups', 'web-exploitation']
description = ''
showLastUpdated = true
+++

Similar to [this lab](/training/portswigger/sqli/sqli_practitioner3), our goal is to extract data from the table `users`, with `username` and `password` stored *in plaintext*.

First, we will need to find out how many columns does the "filter table" contain:
![](/images/viber_image_2026-07-15_09-27-01-867.png)
Again, there are two columns which is *convenient* for us as `users` also has two columns. What about the data types hold by those two columns?
![](/images/viber_image_2026-07-15_09-27-17-694.png)
Unfortunately, not all columns can hold string data which is kinda bad news for us. Though it does return a 500 which means it's processing our injected query server-side. Let's find out which of the two columns holds those strings:
![](/images/viber_image_2026-07-15_09-27-31-439.png)
So the silver lining is that *one* column can hold strings. However, `users` has two values to be fetched, meaning we will have to somehow combine two entries into one. This can be done by string concatenation, whereas `username` and `password` will be added together, separated by an unique symbol.

The syntax for string concatenation varies between database types, in this case for Oracle it's as follow:
```
'foo'||'bar'
```

Knowing this, we can inject the following query:
![](/images/viber_image_2026-07-15_09-28-59-656.png)