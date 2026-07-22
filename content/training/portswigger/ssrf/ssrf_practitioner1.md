+++
date = '2026-07-21T14:51:16+08:00'
draft = false
title = "SSRF with blacklist-based input filter | <span style=\"color:#3498db\">Practitioner</span>"
tags = ['training', 'PortSwigger', 'writeups', 'web-exploitation']
description = ''
showLastUpdated = true
+++

This lab has a stock check feature which fetches data from an internal system, below is how the intercepted request looks like:
![](/images/viber_image_2026-07-21_15-11-00-694.png)

The lab hints that there is an admin interface at `http://localhost/admin`, and that there are two anti-SSRF defenses in place which we must bypass. And indeed there is a filter in use, straight up requesting for `localhost/admin` will result in the request being blocked. This is also the case for only `http://localhost`:
![](/images/viber_image_2026-07-21_14-53-27-599.png)

However, dishing out arbitrary URLs into the `stockApi` param is surprisingly "accepted", evident by the 500 shown here:
![](/images/viber_image_2026-07-21_14-57-25-893.png)
But the same cannot be said for the keyword `admin`. If `admin` is present, the request will be blocked whatever the context. So that's our first anti-SSRF defense found:
![](/images/viber_image_2026-07-21_15-01-43-775.png)
But the blacklist is very literal, as it doesn't check for case sensitivity and whatnot. So literally replacing `admin` with `aDmIn` will do the trick.

As for `localhost`, we can actually use an alternative IP representation such as `127.1` and it will work fine:
![](/images/viber_image_2026-07-21_15-02-20-646.png)
Thus we have found the two anti-SSRF defenses. Using all the information, we can send a request to `http://127.1/aDmIn` to access the administrator panel as follow:
![](/images/viber_image_2026-07-21_15-09-07-140.png)