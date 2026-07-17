+++
date = '2026-07-15T10:01:16+08:00'
draft = false
title = "SQL injection attack, querying the database type and version on Oracle | <span style=\"color:#3498db\">Practitioner</span>"
tags = ['training', 'PortSwigger', 'writeups', 'web-exploitation']
description = ''
showLastUpdated = true
+++

In this lab, we are tasked with finding the version of an Oracle database. The query syntax is as follow:
```sql
SELECT banner FROM v$version
SELECT version FROM v$instance
```

First, we will need to find out [how many columns there are](/training/portswigger/sqli/sqli_practitioner1). One thing about Oracle is that every `SELECT` must use the `FROM` keyword and specify a valid table. Since we are probing for `NULL`s, we should query from the built-in table `DUAL` otherwise our injected query will return 500:
![](/images/viber_image_2026-07-15_10-10-07-263.png)
We now know that there are two columns in the filter table, we can then use either query syntax above to probe for the Oracle version as follow:
![](/images/viber_image_2026-07-15_10-11-22-986.png)