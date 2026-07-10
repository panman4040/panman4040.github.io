+++
date = '2026-07-08T13:46:16+08:00'
draft = false
title = 'Exploiting clickjacking vulnerability to trigger DOM-based XSS'
tags = ['training', 'PortSwigger', 'writeups', 'web-exploitation']
description = ''
showLastUpdated = true
+++
As the lab's name suggests, there exists a DOM-XSS vulnerability somewhere in the application. Naturally, we should crawl through the site and see where does a vulnerable DOM sink lives. The feedback form is looking a bit fishy so let's check that out!
![](/images/viber_image_2026-07-08_13-30-36-341.png)
Looks like our intuition is correct! Whatever passed into the `name` field *will be* reflected in the `innerHTML` sink after submission. And it looks like the application doesn't filter out angle brackets or quotes, which makes constructing XSS payloads much easier. Let's test this out by injecting a harmless `h1`:
![](/images/viber_image_2026-07-08_13-33-07-835.png)
The parser indeed recognises this as valid HTML, and display it proudly. Since our sink is `innerHTML`, we have to use `img` or `iframe` to fire our events. Testing with the `<img src=1 onerror=print()>` payload will do its job as told. 

Since the lab has no authentication, we can safely assume that one does not need to verify his cookies to send feedbacks. But just to be safe, we should check whether the session cookie has `SameSite` set to `None` or not, and it looks like it does:
![](/images/viber_image_2026-07-08_13-46-23-477.png)
With all these in mind, we can use Burp Clickbandit to set up a quick PoC and solve the lab!