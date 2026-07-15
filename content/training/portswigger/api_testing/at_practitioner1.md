+++
date = '2026-07-09T10:32:16+08:00'
draft = false
title = "Finding and exploiting an unused API endpoint | <span style=\"color:#3498db\">Practitioner</span>"
tags = ['training', 'PortSwigger', 'writeups', 'web-exploitation']
description = ''
showLastUpdated = true
+++

Our task is to purchase the hackerleet hoodie, but we are also broke af. Upon attempting to purchase the thing without septims, we are hit with an expected denial:
![](/images/viber_image_2026-07-09_10-12-01-715.png)

One may think to try illicitly manipulate our store balance, but there is no JS or API call present that queries for an user's current balance. So that's bust.

However, notice how whenever we fetch a GET request to a store item, an API request to `/products/./price` is called:
![](/images/viber_image_2026-07-09_10-11-59-974.png)
Obviously, if we are not able to increase our coffers, we should make the shopitem free right? Since the response body is a JSON object, it's expected that the request body to change the price should be a JSON object too. Let's try sending a POST request to change the price to zero:
![](/images/viber_image_2026-07-09_10-20-03-319.png)
Too bad that we are hit with a 405, but there is one convenient header they so kindly left for us. The only allowed methods are `GET`, which we've just covered, and `PATCH`. Since we cannot create a new resource with `POST`, let's try **updating** the resource with `PATCH` instead:
![](/images/viber_image_2026-07-09_10-24-19-920.png)
Looks like that didn't work. It's sad to say but I've spent an embarassingly long amount of time trying to figure out what went wrong here, but in reality it's just that the `price` is a **string**, and the backend demands an **integer**:
![](/images/viber_image_2026-07-09_10-31-16-323.png)
So after setting the price of leet to free, we are able to buy it no problemo:
![](/images/viber_image_2026-07-09_10-31-40-054.png)
![](/images/viber_image_2026-07-09_10-31-50-972.png)